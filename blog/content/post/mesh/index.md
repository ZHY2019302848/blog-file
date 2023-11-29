---

title: "Mesh"
description: 
date: 2023-11-28T11:02:09+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories: 
    - 实践

---

# 清洗UrbanBIS数据
把Longhua Qingdao Wuhu Yingrenshi Yuehai五个城市的可用房屋mesh数据以及其对应的材质图片提取出来

现在有可用的房屋mesh数据862个。

## 本周PLAN

### 删除无用纹理图片

写一个py脚本，先存储每一个mtl文件中提到的jpg文件，之后在wenli文件夹下搜索，只要是没有使用过的图片就删除,去除无用数据。

```python


#"E:/UrbanBIS_useful/test"

import os
import re

def extract_texture_names(mtl_path):
    texture_names = set()
    with open(mtl_path, 'r') as mtl_file:
        for line in mtl_file:
            match = re.match(r'map_Kd\s+\.\.\/\.\.\/wenli\/([^\s]+\.jpg)', line)
            if match:
                texture_name = match.group(1)
                texture_names.add(texture_name)
    return texture_names

def delete_unused_textures(wenli_dir, used_texture_names):
    for texture_file in os.listdir(wenli_dir):
        if texture_file.lower().endswith('.jpg') and texture_file not in used_texture_names:
            texture_path = os.path.join(wenli_dir, texture_file)
            # Delete the texture file
            os.remove(texture_path)
            print(f"Deleted: {texture_path}")

def process_building_folder(root_dir, building_folder):
    building_path = os.path.join(root_dir, building_folder, 'building')

    # Process each sub-building folder
    all_used_texture_names = set()

    for sub_building_folder in os.listdir(building_path):
        sub_building_folder_path = os.path.join(building_path, sub_building_folder)

        # Check if the item is a directory
        if os.path.isdir(sub_building_folder_path):
            mtl_files_path = sub_building_folder_path

            # Process the MTL files
            used_texture_names = set()

            for mtl_file_name in os.listdir(mtl_files_path):
                if mtl_file_name.endswith(".mtl"):
                    mtl_file_path = os.path.join(mtl_files_path, mtl_file_name)
                    used_texture_names.update(extract_texture_names(mtl_file_path))

            # Merge used texture names from each sub-building folder
            all_used_texture_names.update(used_texture_names)

    # Print the merged used texture names
    print(f"Used texture names in {building_folder}: {all_used_texture_names}")

    # Delete unused textures in the wenli directory
    wenli_dir = os.path.join(root_dir, building_folder, 'wenli')
    delete_unused_textures(wenli_dir, all_used_texture_names)

if __name__ == "__main__":
    root_directory = "E:/UrbanBIS_useful/mesh/Yuehai"  # Adjust this based on your directory structure

    # Iterate through all subdirectories in the "Longhua" folder
    for building_folder in os.listdir(root_directory):
        building_folder_path = os.path.join(root_directory, building_folder)

        # Check if the item is a directory
        if os.path.isdir(building_folder_path):
            process_building_folder(root_directory, building_folder)

```

### 对建筑进行划分

好像只能纯手工去干，没想到什么好方法

### 给之前的Building3D建筑加纹理 :（

继续学opengl，但感觉可以用另一种方法解决。

### 搜集多目标追踪的评价指标相关内容，并制作ppt
