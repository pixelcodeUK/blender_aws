# Blender Rendering in AWS

## SSH into EC2

```sh
ssh -i cert.pem ubuntu@ec2-123.amazonaws.com
```

## Setup script

Install Blender in an Ubuntu AMI:

```sh
sudo add-apt-repository ppa:thomas-schiex/blender -y
sudo apt-get update -y
sudo apt-get install blender -y
sudo apt-get install unzip -y
sudo apt-get install zip -y
sudo apt-get install awscli -y
```

## Running a test render

Download a test Blender file:

```sh
wget "https://download.blender.org/demo/test/BMW27_2.blend.zip"
unzip BMW27_2.blend.zip -d ./
```

## Running Blender

Run Blender in non-UI mode:

```sh
blender --background ./bmw27/bmw27_cpu.blend  --use-extension 1 --threads 0 --render-output ./frames/frame_ --frame-start 1 --frame-end 1 --render-anim
```

NB: The [order of the arguments](https://docs.blender.org/manual/en/dev/render/workflows/command_line.html) matter

## Modifying a Blender file before rendering

```sh
blender --background ./bmw27/bmw27_cpu.blend --python script.py --use-extension 1 --threads 0 --render-output ./frames/frame_ --frame-start 1 --frame-end 1 --render-anim
```

Where script.py runs inside Blender.

### Example python script

```python
import bpy

#Choose a renderer
bpy.context.scene.render.engine = 'CYCLES'
#bpy.context.scene.render.engine = 'BLENDER_RENDER'

#Choose render type
bpy.context.scene.cycles.device = 'CPU'
#bpy.context.scene.cycles.device = 'GPU'

#Choose number of samples
bpy.context.scene.cycles.samples = 300

#Tile size
bpy.context.scene.render.tile_x = 32
bpy.context.scene.render.tile_y = 32

#Resolution
bpy.context.scene.render.resolution_x = 1920
bpy.context.scene.render.resolution_y = 1080


#Resolution percentage
bpy.context.scene.render.resolution_percentage = 50

```

## Zipping up animation frames

```sh
zip files.zip ./frames/*
```

Zip up all files inside the frames folder.

## Moving the zip to an S3 bucket

```sh
aws s3 cp files.zip s3://my-bucket --region us-east-1
```

## Combining all data in .blend file

Make sure **File > External Data > Automatically pack into .blend** is checked to include assets into the .blend file.

## Copying a local file to EC2

```sh
scp -i cert.pem ./myfile.blend ubuntu@ecu-123.amazonaws.com:~/
```

Copy **myfile.blend** to the home folder in the EC2.

## Copying a remote file to local machine

```sh
scp -i cert.pem ubuntu@ecu-123.amazonaws.com:~/frames/frame_0001.png ./frame_0001.png
```

Copy **frame_0001.png** to local machine.
