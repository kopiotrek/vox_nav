### TEMPORARY WORK IN PROGRESS SECTION

Running:

 docker build -t vox_nav -f vox_nav/docker/Dockerfile . --no-cache
 docker build -t vox_nav --no-cache --progress=plain -f vox_nav/docker/Dockerfile . 2>&1 | tee build.log

docker run -it --network=host --ipc=host \
-e DISPLAY \
-e QT_X11_NO_MITSHM=1 \
-e RMW_IMPLEMENTATION=rmw_fastrtps_cpp \
-e NVIDIA_VISIBLE_DEVICES=all \
-e NVIDIA_DRIVER_CAPABILITIES=all \
-e ROS_DOMAIN_ID \
-v /tmp/.X11-unix:/tmp/.X11-unix \
-v /dev/dri:/dev/dri \
vox-nav

#TODO:
Add parallel building





Build problems:

#14 78.49 cp: cannot stat 'install/ompl/lib/libompl.so*': No such file or directory

#14 140.6 /root/app/src/vox_nav/vox_nav_utilities/include/vox_nav_utilities/planner_helpers.hpp:20:10: fatal error: ompl/base/samplers/ObstacleBasedValidStateSampler.h: No such file or directory
#14 140.6    20 | #include <ompl/base/samplers/ObstacleBasedValidStateSampler.h>
#14 140.6       |          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~




PROBLEMS TO BE ISSUED:
1. 
cp install/ompl/lib/libompl.so* /usr/local/lib/; \
cp install/casadi/lib/libcasadi.so* /usr/local/lib/; \  

had to be changed to:

cp install/ompl/share/libompl.so* /usr/local/lib/; \
cp install/casadi/lib/libcasadi.so* /usr/local/lib/; \  

2.
problem with paths to ompl files solved with:

RUN mv /opt/ros/$ROS_DISTRO/include/ompl-1.6/ompl /opt/ros/$ROS_DISTRO/include && \
    rm -rf /opt/ros/$ROS_DISTRO/include/ompl-1.6

3. 
fast_gicp was not included in underlay.repos, but needed by vox_nav_misc packages

4.



# vox_nav
A navigation framework for outdoor robotics in rough uneven terrains.

![humble](https://github.com/jediofgever/vox_nav/workflows/humble/badge.svg)  

![foxy](https://github.com/jediofgever/vox_nav/workflows/foxy/badge.svg)  

### Documenation

Please note that the documentation is under Construction!, so some parts might be missing.

https://nmburobotics.github.io/vox_nav/#/


### Videos 

* A video with a better resolution [Thorvald Navigation with vox_nav](https://www.youtube.com/watch?v=LIhPUCxiOAg) 

### Related Publications

If using vox_nav for scientific publications, please consider citing the following papers.

```bash

@INPROCEEDINGS{9981647,
  author={Atas, Fetullah and Cielniak, Grzegorz and Grimstad, Lars},
  booktitle={2022 IEEE/RSJ International Conference on Intelligent Robots and Systems (IROS)}, 
  title={Elevation State-Space: Surfel-Based Navigation in Uneven Environments for Mobile Robots}, 
  year={2022},
  volume={},
  number={},
  pages={5715-5721},
  doi={10.1109/IROS47612.2022.9981647}}

@article{DBLP:journals/corr/abs-2103-13666,
  author    = {Fetullah Atas and
               Lars Grimstad and
               Grzegorz Cielniak},
  title     = {Evaluation of Sampling-Based Optimizing Planners for Outdoor Robot
               Navigation},
  journal   = {CoRR},
  volume    = {abs/2103.13666},
  year      = {2021},
  url       = {https://arxiv.org/abs/2103.13666},
  archivePrefix = {arXiv},
  eprint    = {2103.13666},
  timestamp = {Wed, 07 Apr 2021 15:31:46 +0200},
  biburl    = {https://dblp.org/rec/journals/corr/abs-2103-13666.bib},
  bibsource = {dblp computer science bibliography, https://dblp.org}
}
```

### Credits

* A lot of architectural aspects of this project has been inspired by the [Navigation2.](https://github.com/ros-planning/navigation2).
We greatly appreciate the efforts of [Navigation2.](https://github.com/ros-planning/navigation2) community for providing such high quality software design to Robotics community.

* This systems relies on libraries e.g. [OMPL](https://github.com/ompl/ompl), [Casadi](https://github.com/casadi/casadi), [Octomap](https://github.com/OctoMap/octomap)
  and many more, they cant all be listed here, we appriciate and recognize the efforts of people involved in development of these libraries.
