build-image:
	docker build -t agimenezibanez/nameko-example-products:$(TAG) .;

docker-tag:
	docker tag agimenezibanez/nameko-example-products:$(FROM_TAG) \
	agimenezibanez/nameko-example-products:$(TAG)

push-image:
	docker push agimenezibanez/nameko-example-products:dev
