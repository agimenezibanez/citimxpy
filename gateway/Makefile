build-image:
	docker build -t agimenezibanez/nameko-example-gateway:$(TAG) .;

docker-tag:
	docker tag agimenezibanez/nameko-example-gateway:$(FROM_TAG) \
	agimenezibanez/nameko-example-gateway:$(TAG)

push-image:
	docker push agimenezibanez/nameko-example-gateway:dev
