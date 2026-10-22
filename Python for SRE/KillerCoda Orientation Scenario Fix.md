(need naa sa directory nga naa ang dockerfile)
docker build -t <image:tag> .

docker save <image:tag> | ctr -n k8s.io images import -
ctr -n k8s.io images list | grep <image_name>

(update ang helm values nya upgrade)