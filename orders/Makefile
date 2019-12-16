build-image:
	docker build -t agimenezibanez/nameko-example-orders:$(TAG) .;

docker-tag:
	docker tag agimenezibanez/nameko-example-orders:$(FROM_TAG) \
	agimenezibanez/nameko-example-orders:$(TAG)

push-image:
	docker push agimenezibanez/nameko-example-orders:dev
