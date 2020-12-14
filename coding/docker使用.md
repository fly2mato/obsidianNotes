item2 中打开docker container：
```bash
docker exec -it {containerID} sh
```

查看并打开历史container
```bash
docker container ls -a
docker start {container_id}
docker attach {container_id} 
```