
display := $(shell echo $${DISPLAY})
home ?= $(shell echo ~)
image_name := vtk-base
command := /opt/Qt/Tools/QtCreator/bin/qtcreator
local_user_id := ${shell echo $${UID}}
working_dir := $(shell pwd)

build:
	docker build \
		--build-arg LOCAL_USER_ID=${local_user_id} \
		-t ${image_name} .

run:
	docker run \
		--shm-size=256m \
		--gpus all \
		--env DISPLAY=${display} \
		--env QT_X11_NO_MITSHM=1 \
		-v /dev/shm:/dev/shm \
		-v /tmp/.X11-unix:/tmp/.X11-unix \
		-v ${working_dir}/config:/home/developer/.config/QtProject \
		-v ${home}:${home} \
		--entrypoint ${command} \
		-it ${image_name}
