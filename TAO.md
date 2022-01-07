TAO overview
==
TAO provides a software framework which allows cameras to communicate with other machines for real-time adaptive optics applications. It consists of several modules  
1. Unified camera implementation
2. Shared Resource (Inter-process communication)
3. Camera server

The diagram below roughly shows how TAO works

![alt text][tao_diagram]

[tao_diagram]: ./fig/tao_rt_architecture.png

A TAO camera has a *tao_camera_ops* data field which contains unified camera operation table. These operations wrap camera's APIs in TAO architecture to hide the physical layer. The TAO camera transfers data using *tao_acquisition_buffer* struct and holds camera's meta data in *tao_camera_info*.

A client is a process that attempts to interact with the TAO camera. The client reaches to an **access point** of a camera server to send requests. The camera server then invokes a camera operation according to the request.

A TAO camera shares data and camera information through shared memory space. A frame grabber is a struct that handles data transfer. The frame grabber contains a vector of shared arrays. Each array contains data of an image taken by the TAO camera. A shared camera is a struct that exposes TAO camera information to the outside world.

The image acquisition is started upon a request from a client. The camera server spawns a new worker process to handle image acquisition. It monitors and manages the shared resources using mutex and monitor synchronization. There are 2 parallel processes taking place during image acquisition. *tao_wait_acquisition_buffer* waits until a TAO camera has filled in *tao_acqusition_buffer*. Then, it processes the image frame by writing data to a shared array in the framegrabber list and  timestamps. The next frame and last frame shmids are posted and ready for clients to query via the server.
