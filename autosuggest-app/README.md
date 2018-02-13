export PROJECT_NAME="autosuggest"
export PROJECT_ID="autosuggest-194816"
export IMAGE_NAME="autosuggest-app"
export IMAGE_VERSION="v0.1.0"
export CONTAINER_NAME="autosuggest-app"

docker build -t eu.gcr.io/$PROJECT_ID/$IMAGE_NAME:$IMAGE_VERSION .

docker run -it --rm -p 8080:8080 --name $CONTAINER_NAME eu.gcr.io/$PROJECT_ID/$IMAGE_NAME:$IMAGE_VERSION

gcloud docker -- push eu.gcr.io/$PROJECT_ID/$IMAGE_NAME:$IMAGE_VERSION
