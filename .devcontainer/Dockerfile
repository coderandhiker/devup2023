FROM chrisrt/aijourney:latest
RUN apt-get update && \
    apt-get install -y python3-dev python3-pip

RUN pip3 install protobuf==3.20.0 && \
    pip3 install tensorflow==2.5.0 && \
    pip3 install numpy==1.21 && \
    pip3 install matplotlib==3.4.3 && \
    pip3 install git+https://github.com/openai/CLIP.git

RUN pip3 install torch