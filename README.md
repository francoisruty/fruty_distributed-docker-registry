This repo refers to https://fruty.io/2018/10/01/docker-registry-distribution-in-a-deep-learning-pipeline/

### INTRODUCTION

This repo shows you how to deploy a docker registry backed by a S3 bucket for storage, meaning you can distribute the registry horizontally (deploy many more containers with same config), since all those instances will be backed for storage by the same S3 bucket.

This is pretty useful for security and operational resiliency (all the docker images you built are on S3 which means very resilient storage, you'll never lose your data), but also when you have a lot of processes constantly interacting with the registry.
For example, in a computer vision deep learning pipeline, you will have probably a cluster executing many processing tasks all the time, and if you use Docker everywhere, you end up having tasks frequently downloading huge machine learning docker images. If you have only 1 registry container, traffic will get slow very quickly.

With this tuto, you can easily distribute your docker registry. This is a low-cost / low-tech solution, *very* efficient in a top-down setup (many processes pulling images from the registry)
This of course does not guarantee consistency if you have 2 processes updating the same docker image via different registry instances.


### PREPARATION

First of all, choose a region (ex: eu-central-1) and create an S3 bucket with your AWS account.

Then, enter those 2 parameters in config.yml ( {{your_region}} and {{your_bucket_name}})
Now update the .env with your AWS API credentials (AWS_ACCESS_KEY and AWS_SECRET_KEY).

### TEST

docker-compose up -d

Push an image to the first registry (127.0.0.1:5500)

Then you should see this image when you query the 2nd registry with
curl -X GET 127.0.0.1:6500/v2/_catalog

Congratulations! You now have 2 docker registries, that use the same infinitely scalable storage backend (your S3 bucket).
