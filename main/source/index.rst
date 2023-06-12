############################################################
Vitis Video Analytics SDK Overview
############################################################

The Vitis Video Analytics SDK **(VVAS)** is a framework to build AI and transcoding solutions on AMD platforms. It takes input data - from USB/CSI camera, video from file or streams over RTSP, and uses Vitis AI to generate insights from pixels for various use cases. VVAS SDK can be the foundation layer for several video analytic solutions like understanding traffic and pedestrians in smart city, health and safety monitoring in hospitals, self-checkout, and analytics in retail, detecting component defects at a manufacturing facility and others. VVAS can also be used to build Adaptive Bitrate Transcoding solutions that may require re-encoding the incoming video at different bitrates, resolution and encoding format. 

The core SDK consists of highly optimized VVAS Core C-APIs as well as GStreamer plug-ins that use various accelerators such as Video Encoder, Decoder, Image_processing kernel (for resize and color space conversion), Deep learning Processing Unit (DPU) for Machine Learning etc. By performing all the compute heavy operations in dedicated accelerators, VVAS can help in building highest performance pipelines for video analytics, transcoding and several other application areas. 

For the developer community, VVAS also provides a framework in the form of generic Infrastructure GStreamer plug-ins and a simplified interface to develop their own acceleration library to control a custom hardware accelerator. Using this framework, user can easily integrate their custom accelerators/kernels into Gstreamer framework. VVAS builds on top of Xilinx Run Time (XRT) and Vitis AI and abstracts these complex interfaces, making it easy for developers to build video analytics and transcoding pipelines without having to learn the complexities of XRT, Vitis AI.

Using VVAS SDK, applications can be deployed on an embedded edge devices or can be deployed on larger edge or datacenter platforms like Alveo V70.

.. figure:: /docs/images/VVA_TopLevel_Overview.png
   :width: 1300

   VVAS Block Diagram

************************
VVAS Graph Architecture
************************

VVAS is an optimized graph architecture built using the open source GStreamer framework. The graph below shows a typical video analytic application starting from input video to outputting insights. All the individual blocks are various plugins that are used. At the bottom are the different hardware engines that are utilized throughout the application. Optimum memory management with zero-memory copy between plugins and the use of various accelerators ensure the highest performance.

.. figure:: /docs/images/VVA_Graph.png
   :width: 1100

* Streaming data can come over the network through RTSP or from a local file system or from a camera directly. The captured frames are sent for decoding using the hardware accelerated video decoder. ``vvas_xvideodec``, ``omxh264dec`` and ``omxh265dec`` are the plugin for decoding. 
* After decoding, there is an optional image pre-processing step where the input image can be pre-processed before inference. The pre-processing can be resizing the image or color space conversion, mean subtrction etc.. ``vvas_xabrscaler`` plugin can perform hardware accelerated resize as well as color format conversion on the frame.
* After pre-processing, frame is sent for inference. Inference is performed using ``vvas_xinfer`` plug-in that performs pre-processing as well as ML inferencing in batch mode to give improved performance.
* To overlay the inference results such as bounding boxes, labels. arrows etc., there are two plug-ins, ``vvas_xmetaconvert`` and ``vvas_xoverlay``. ``vvas_xmetaconvert`` understands the ``VVASInference`` meta data structure and it does parsing of this information and creates new meta data for lines, arrowa, text etc. that are understood by ``vvas_xoverlay`` plug-in. ``vvas_xoverlay`` plug-in draws the bounding box, arrows, circles, text etc. depending on the information it receives.
* Finally, to output the results, VVAS presents various options, like render the output with the bounding boxes on the screen, save the output to the local disk, stream out over RTSP.



.. toctree::
   :maxdepth: 3
   :caption: Release Notes
   :hidden:

   Release Notes <docs/release_notes>

.. toctree::
   :maxdepth: 3
   :caption: Base Infrastructure
   :hidden:

   VVAS GStreamer Plugins <docs/common/gstreamer_plugins/common_plugins>
   VVAS Meta Data <docs/common/meta_data/vvas_meta_data_structures>
   VVAS For Advanced Developers <docs/common/for_developers>
   VVAS Debug Support <docs/common/debug_support>

.. toctree::
   :maxdepth: 3
   :caption: Embedded
   :hidden:

   Platforms & Applications <docs/Embedded/platforms_and_applications>
   VVAS GStreamer Plugins <docs/Embedded/embedded-plugins>
   Tutorials <docs/Embedded/Tutorials/Tutorials>

.. toctree::
   :maxdepth: 3
   :caption: Data Center
   :hidden:

   Platforms & Applications <docs/DC/platforms_and_applications>

.. toctree::
   :maxdepth: 3
   :caption: FAQ
   :hidden:

   Frequently Asked Questions <docs/freq_asked_questions>

.. toctree::
   :maxdepth: 2
   :caption: Other Versions
   :hidden:

   2.0 <https://xilinx.github.io/VVAS/2.0/build/html/index.html>
   1.1 <https://xilinx.github.io/VVAS/1.1/build/html/index.html>
   1.0 <https://xilinx.github.io/VVAS/1.0/build/html/index.html>

Why VVAS?
============

Application developers can build seamless streaming pipelines for AI-based video and image analytics, complex Adaptive Bitrate Transcoding pipelines and several other solutions using VVAS without having any understanding about FPGA or other development environment complexities. VVAS ships with several hardware accelerators for various functionalities, highly optimized GStreamer plugins meeting most of the requirements of the Video Analytics and transcoding solutions.
For advanced developers, VVAS provides an easy-to-use framework to integrate their own hardware accelerators/kernels in to Gstreamer framework-based applications.  
VVAS provide AI model support for popular object detection and classification models such as SSD, YOLO etc.
All these infrastructure gives the flexibility for rapid prototyping to full production level solutions by significantly reducing time to market for the solutions on AMD platforms. 


VVAS Core Components
====================

This section gives more deeper insight into VVAS. VVAS comprises following core components.

.. figure:: /docs/images/vvas_core_comp.png
   :width: 400


Custom GStreamer Plug-ins
-------------------------------------------------------

These are highly optimized GStreamer plug-ins developed to provide very specific functionality using optimized Kernels and IPs on AMD Platform. Refer to :ref:`VVAS Custom Plug-ins <custom_plugins_label>` for more details about how to use these plug-ins.

Infrastructure GStreamer Plug-ins
------------------------------------------------

These are generic infrastructure GStreamer plug-ins being developed to help users to directly use these plug-ins to integrate their Kernels into GStreamer framework. User need not have in-depth understanding of the GStreamer framework. Refer to :ref:`VVAS Infrastructure Plug-ins <infra_plugins_label>` for more details about how to use these plug-ins. These plug-ins are part of “vvas-gst-plugins” folder in the VVAS sources tree.

Acceleration S/W Libs
-----------------------------------

These are optimized Acceleration s/w libs developed to manage the state machine of the acceleration Kernels/IPs and expose the interface so that these Acceleration s/w libs can be hooked into VVAS Generic Infrastructure Plug-ins. These can be used as reference to develop a new Acceleration s/w lib based on VVAS framework. Details about the vvas acceleration s/w libs and how can these be used with infrastructure plugins are explained in :ref:`VVAS Infrastructure Plug-ins and Acceleration s/w Libraries <infra_plugins_label>` section.

Acceleration H/W (Kernels/IPs)
------------------------------------------------

These are highly optimized Kernels being developed by AMD. Details of these are captured in this section. Refer to VVAS Acceleration H/W :doc:`VVAS Acceleration H/W  <docs/common/Acceleration-Hardware>` for more details.

Reference Platforms and Applications
------------------------------------------

There are different requirements of different applications. VVAS provides several reference platforms catering to different applications/solutions needs. Embedded platforms example designs and sample application details can be found in :doc:`Platforms And Applications <docs/Embedded/platforms_and_applications>` section. Similarly Platforms and application details for PCIe/Data center are covered in :doc:`PCIe/Data Center Platforms and Applications <docs/DC/platforms_and_applications>`
