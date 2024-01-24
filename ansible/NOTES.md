docker pull ansible/ansible:latest
docker run --rm -it -v $(pwd):/ansible -v ~/.ssh/id_rsa:/root/id_rsa ansible/ansible:latest play.yml
docker build -t ansible_image . &&  docker run -p 4000:80 ansible_image