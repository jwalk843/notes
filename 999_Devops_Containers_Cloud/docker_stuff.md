Docker Stuff
============

# Get a shell within the container
docker ps
sudo docker exec -u 0 -it <id_container> bash

# Investigating container
# --no-trunc option allow to have full output
sudo docker history cont/ainer --no-trunc

# Getting the container filesystem from the host
# Then chmod and extract it
# It can be usefull if some files have been deleted
sudo docker save cont/ainer -o container.tar
