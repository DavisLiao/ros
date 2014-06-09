# docker build -t opencog/ros-indigo-blender .
# xhost +
# docker run --rm --privileged -i -v /dev/ttyACM0:/dev/ttyACM0 -v /tmp/.X11-unix/X0:/tmp/.X11-unix/X0 -v /dev/dri:/dev/dri -v /dev/shm:/dev/shm -e DISPLAY=:0.0 -p 33433:33433/udp -t opencog/ros-indigo-blender 
# Note: port 33433=faceshift, /dev/ttyACM0=pololu, /dev/dri=gpu, /dev/shm=gpu
### Blender: click Run Script
### Blender: right-click & drag target (blue cube)
# ToDo: add MORSE robot simulator
#       replace faceshift with OpenCV FaceRecognizer, etc.

FROM opencog/ros-indigo-dev
MAINTAINER Mandeep Singh Bhatia "mandeep.singh.bhatia@gmail.com"
MAINTAINER David Hart "dhart@opencog.org"

RUN apt-get -y update
RUN apt-get -y install blender

RUN apt-get -y install python3-yaml python3-pip
RUN pip3 install rospkg catkin_pkg

RUN echo source /catkin_ws/devel/setup.bash >> /.bashrc && \
    echo -e "\e[1;34m[$SELF_NAME] catkin devel setup\.bash sourced\e[0m"

WORKDIR /catkin_ws/src
RUN cp /opt/ros/indigo/setup.sh /etc/profile.d/ros_indigo.sh
ENV PYTHONPATH /opt/ros/indigo/lib/python2.7/dist-packages
RUN /usr/bin/python3 /opt/ros/indigo/bin/catkin_init_workspace

RUN git clone https://github.com/hansonrobotics/robo_blender
RUN git clone https://github.com/yantrabuddhi/ros_pololu_servo
RUN git clone https://github.com/yantrabuddhi/ros_nmpt_saliency
RUN git clone https://github.com/yantrabuddhi/ros_faceshift
RUN git clone https://github.com/yantrabuddhi/simple_face_tracker

WORKDIR /catkin_ws
RUN bash -l -c "/usr/bin/python3 /opt/ros/indigo/bin/catkin_make"

WORKDIR /catkin_ws/src/robo_blender/src

ENV STARTSCRIPT "\
echo evaluating startup script... &&\
source /catkin_ws/devel/setup.bash &&\
tmux new-session -d roscore &&\
tmux bind-key R respawn-window &&\
tmux set -g set-remain-on-exit on &&\
tmux split-window -d -v -p 25 -t 0 'sleep 2 && rosrun ros_pololu_servo ros_pololu_servo_node' &&\
tmux split-window -d -v -p 25 -t 0 'sleep 4 && rosrun ros_faceshift ros_faceshift_node.py' &&\
tmux split-window -d -v -p 25 -t 0 'sleep 6 && blender robo.blend' &&\
tmux split-window -d -v -p 25 -t 0 'sleep 6 && bash -l -i' &&\
tmux attach \
"

CMD /bin/bash -l -c "eval $STARTSCRIPT"