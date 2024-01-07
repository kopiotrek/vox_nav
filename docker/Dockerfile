## Build with:
##   docker build -t lcas.lincoln.ac.uk/lcas/vox_nav:docker .
##
## Run with:
##   docker-compose up
##
## Kill with:
##   docker-compose down
##
## Build with log:
## docker build --no-cache --progress=plain -f vox_nav/docker/Dockerfile . 2>&1 | tee build.log
## or
## docker build --progress=plain . 2>&1 | tee build.log
##
## Push with:
## docker push kopiotrek/vox_nav:latest


# docker build -t vox_nav -f vox_nav/docker/Dockerfile .

# docker run -it --network=host --ipc=host \
# -e DISPLAY \
# -e QT_X11_NO_MITSHM=1 \
# -e RMW_IMPLEMENTATION=rmw_fastrtps_cpp \
# -e NVIDIA_VISIBLE_DEVICES=all \
# -e NVIDIA_DRIVER_CAPABILITIES=all \
# -e ROS_DOMAIN_ID \
# -v /tmp/.X11-unix:/tmp/.X11-unix \
# -v /dev/dri:/dev/dri \
# vox-nav


ARG ROS_DISTRO=humble
FROM osrf/ros:humble-desktop

RUN apt-get update; apt-get -y upgrade



# Creating app
RUN mkdir -p /app/src
RUN /bin/bash -c "source /opt/ros/$ROS_DISTRO/setup.bash && \
                  cd /app/ && \
                  colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=Release"

# Installing tools
RUN apt install -y wget && \
    apt install -y git


# Installing dependecies
RUN . /opt/ros/$ROS_DISTRO/setup.sh; \
    apt install -y python3-colcon-common-extensions && \
    apt install -y python3-rosdep2 && \
    apt install -y ros-$ROS_DISTRO-twist-mux ros-$ROS_DISTRO-navigation2 && \
    apt install -y python3-colcon-common-extensions && \
    apt install -y python3-vcstool && \
    apt install -y xdotool && \
    apt install -y coinor-libipopt-dev && \
    apt install -y libfcl0.7 && \
    apt install -y pkg-config && \
    apt install -y libmosquitto* && \
    apt install -y ros-$ROS_DISTRO-tf2-eigen && \
    rosdep update

 
# Cloning repos and building
COPY vox_nav /app/src/vox_nav
# RUN cd /app && wget https://raw.githubusercontent.com/jediofgever/vox_nav/$ROS_DISTRO/underlay.repos && \
#     vcs import src < underlay.repos --recursive 
 
RUN /bin/bash -c 'cd /app && vcs import src < src/vox_nav/underlay.repos --recursive' 

RUN rosdep update && cd /app && \
    rosdep install -y -r -q --from-paths src --ignore-src --rosdistro $ROS_DISTRO 
# RUN cd /app && vcs pull src 
RUN /bin/bash -c 'mv /opt/ros/$ROS_DISTRO/include/ompl-1.6/ompl /opt/ros/$ROS_DISTRO/include && \
    rm -rf /opt/ros/$ROS_DISTRO/include/ompl-1.6'

RUN /bin/bash -c 'cd /app; \
                  source /opt/ros/$ROS_DISTRO/setup.bash; \
                  rosdep install -y -r -q --from-paths src --ignore-src --rosdistro $ROS_DISTRO; \
                  colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=Release --packages-select ompl casadi; \
                  cp install/ompl/share/libompl.so* /usr/local/lib/; \
                  cp install/casadi/lib/libcasadi.so* /usr/local/lib/; \  
                  source install/setup.bash ; \
                  colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=Release -DACADOS_WITH_QPOASES=ON -DACADO_CODE_IS_READY=ON -DWITH_IPOPT=true --packages-select vox_nav_msgs vox_nav_utilities; \
                  source install/setup.bash ; \
                  colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=Release -DACADOS_WITH_QPOASES=ON -DACADO_CODE_IS_READY=ON -DWITH_IPOPT=true --packages-skip-regex archive --packages-skip vox_nav_control vox_nav_misc; \
                  source /opt/ros/$ROS_DISTRO/setup.bash; \
                  cd /app; \
                  source build/ACADO/acado_env.sh; \
                  source install/setup.bash ; \
                  colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=Release -DACADOS_WITH_QPOASES=ON -DACADO_CODE_IS_READY=ON -DWITH_IPOPT=true --packages-select vox_nav_control; \
                  source /app/install/setup.bash'

RUN /bin/bash -c '. /opt/ros/$ROS_DISTRO/setup.bash; cd /app; \
                  echo "source /app/install/setup.bash" >> /home/.bashrc; \
                  echo "export NO_AT_BRIDGE=1" >> /home/.bashrc'


# colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=Release -DACADOS_WITH_QPOASES=ON -DACADO_CODE_IS_READY=ON -DWITH_IPOPT=true --packages-skip-regex archive

WORKDIR /app
# export NO_AT_BRIDGE fixes an error in the rqt:
# "AT-SPI: Error retrieving accessibility bus address: org.freedesktop.DBus.Error.ServiceUnknown:
# The name org.a11y.Bus was not provided by any .service files"

# RUN cd /tmp && curl -fOL https://github.com/cdr/code-server/releases/download/v3.12.0/code-server_3.12.0_amd64.deb && dpkg -i code-server_3.12.0_amd64.deb && rm code-server_3.12.0_amd64.deb
# RUN bash -c 'echo -e "[supervisord]\nredirect_stderr=true\nstopsignal=QUIT\nautorestart=true\ndirectory=\n\n[program:codeserver]\ndirectory=/home\ncommand=/usr/bin/code-server --auth none --bind-addr 0.0.0.0:8888\nuser=ubuntu\nenvironment=DISPLAY=:1,HOME=/home,USER=ubuntu" > /etc/supervisor/conf.d/codeserver.conf'