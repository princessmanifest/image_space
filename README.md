ImageSpace
==========
ImageSpace is an application built on top of [ImageCat](http://github.com/chrismattmann/imagecat/)
that allows a user to browse a rich catalog of EXIF-metadata extracted and OCR extracted information
from images. It allows histogram and D3-based visual search, free text search and retrieval and 
performs image similarity metrics using computer vision techniques and metadata-techniques (e.g., 
[Jaccard Similarity](http://github.com/chrismattmann/tika-img-similarity)).

Quickstart
==========
ImageSpace can be spun up quickly by means of Docker containers, see the [ImageSpace Quickstart](https://github.com/nasa-jpl-memex/image_space/wiki/Quick-Start-Guide-with-ImageCat).

Image Similarity through [SMQTK](https://github.com/nasa-jpl-memex/SMQTK) can be made available by following the [ImageSpace SMQTK Quickstart](https://github.com/nasa-jpl-memex/image_space/blob/master/imagespace_smqtk/Docker.md).

Installation
============
See the [installation README](https://github.com/nasa-jpl-memex/image_space/tree/master/imagespace) for
instructions.

Credits
=======
ImageSpace is developed by the [JPL-Kitware](http://memex.jpl.nasa.gov/) team funded
through the [DARPA Memex](http://www.darpa.mil/newsevents/releases/2014/02/09.aspx) program.

Questions, comments?
===================
Send them to [Chris A. Mattmann](mailto:chris.a.mattmann@jpl.nasa.gov).

Authors
=======
* Jeff Baumes, Kitware  
* Chris Mattmann, JPL
* Dan LaManna, Kitware
* Harshavardhan Manjunatha, USC
* Lewis John McGibbney, JPL
* Madhav Sharan, JPL & USC

License
===
This project is licensed under the [Apache License, version 2.0](http://www.apache.org/licenses/LICENSE-2.0).

==================================================================
# additional Helpful Instructions for Setting Up ImageSpace + SMQTK (by students for students <3) USC 2025

##Focus on this page (https://github.com/nasa-jpl-memex/image_space/wiki/Quick-Start-Guide-with-ImageCat), but do so by following the steps below:


## Helpful Paths
- **Absolute path to image directory:**  
  `we advise pasting it here`

- **Absolute path to deploy directory:**  
  `we advise pasting it here for quick reference `

---

##Step-by-Step Setup Guide

###  Step -1: Edit Your `docker-compose.yml`
Scroll to the bottom and replace the networks section:

**Replace this:**
```yaml
networks:
  imagespace-network:
    driver: bridge
```

**With this:**
```yaml
networks:
  deploy_imagespace-network:
    external: true
```

---

###  Step 0: Ensure Docker Is Running
Run:
```bash
docker ps
```
If you see `Is the docker daemon running?`, open **Docker Desktop**, let it run in the background, and rerun `docker ps`.

---

###  Step 1: Check Existing Networks
```bash
docker network ls
```

Expected output should include:
```
bridge      bridge    local
host        host      local
none        null      local
```

---

###  Step 2: Navigate to the SMQTK Folder
Using your IDE, go to:  
`ide_project_name → IMAGE_SPACE → imagespace_smqtk`

---

###  Step 3: Run SMQTK Services
```bash
./smqtk_services.run_images.sh --docker-network deploy_imagespace-network --images /Users/cindyjiang/Desktop/GeneratedImages-small
```

 You should see:
```
Creating new docker network "deploy_imagespace-network" using bridge driver
...
```

---

###  Step 4: Verify Network Creation
```bash
docker network ls
```

You should now see:
```
deploy_imagespace-network   bridge    local
```

---

###  Step 5: Check SMQTK Logs
1. Run:
   ```bash
   docker ps
   ```
2. Copy the **CONTAINER ID** of the SMQTK services container.
3. Run:
   ```bash
   docker logs <container_id>
   ```

You should see output ending in:
```
#####################
 Build FLANN index 
#####################
Starting NN Service
Starting IQR service
```

---

###  Step 6: Import Images into Solr
1. Run `docker ps` to find the container name for `imagespace-solr`.
2. Then run:
   ```bash
   ./solr/import-images.sh <SOLR-CONTAINER-NAME> imagespace /Users/cindyjiang/Desktop/GeneratedImages-small
   ```

 *(Optional: Check Postgres logs similarly if needed.)*

---

###  Step 7: Confirm Docker App Status
At this point, **Docker Desktop** should show:

- **Containers:**  
  `smqtk-postgres`, `smqtk-services`

- **Images:**  
  `postgres:9.5.10`, `nasajplmemex/smqtk:latest`

---

###  Step 8: Start Main Containers
Navigate to:  
`ide_project_name → IMAGE_SPACE → scripts → deploy`

Then run:
```bash
IMAGE_DIR=/Users/cindyjiang/Desktop/GeneratedImages-small docker-compose up -d
```

---

###  Step 9: Enable ImageSpace
Still inside the deploy folder, run:
```bash
sh ./imagespace/enable-imagespace.sh
```

 Now open Chrome and go to:  
**`http://localhost:8080`**

Click the **"Help"** tab (top right) for usage instructions.

---

##  Helpful Commands (for Retry or Reset)

- Remove old network:
  ```bash
  docker network rm deploy_imagespace-network
  ```

- View running containers:
  ```bash
  docker ps
  ```

- Clone the full repo:
  [https://github.com/nasa-jpl-memex/image_space](https://github.com/nasa-jpl-memex/image_space)

---

##  Final Notes

- **Do NOT manually create** the network with:
  ```bash
  docker network create deploy_imagespace-network
  ```
  The SMQTK script already handles that.

- If you make changes to your `.yml`, you must:
  1. Delete all non-default networks and containers
  2. Trash Docker images
  3. Clear browser cache
  4. (Optional) Re-clone the GitHub repo from scratch

---


