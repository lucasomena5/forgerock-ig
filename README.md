# forgerock-ig
docker build . -t ig:v2023-v5-sample
docker image tag ig:v2023-v5-sample devforge1/ig:v2023-v5-sample
docker rmi ig:v2023-v5-sample
docker push devforge1/ig:v2023-v5-sample