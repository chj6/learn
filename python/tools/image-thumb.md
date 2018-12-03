# 生产jpg的缩略图
``` python
# coding=utf-8
import glob
import os
import concurrent.futures
from PIL import Image

# 需要压缩的图片源文件夹目录
source_image_path = "/Users/sam/*"
thumb_image_path = "/Users/sam/thumb/"

def make_image_thumbnail(filename):
    source_filename = os.path.split(filename)[1]
    thumb_filename, file_extension = os.path.splitext(source_filename)
    thumb_filename_full = thumb_image_path + f"{os.path.split(thumb_filename)[1]}_thumbnail{file_extension}"
    # 创建和保存缩略图
    image = Image.open(filename)
    image.thumbnail(size=(128, 128))
    image.save(thumb_filename_full, "JPEG")
    # 图片压缩，压缩质量还是挺不错的
    # image.save(thumb_filename_full, "JPEG", quality=10)
    return thumb_filename_full


# 循环文件夹中所有JPEG图像，为每张图像创建缩略图
for image_file in glob.glob(source_image_path):
    thumbnail_file = make_image_thumbnail(image_file)

print(f"success end")

# 创建Process Pool，默认为电脑的每个CPU创建一个
# with concurrent.futures.ProcessPoolExecutor() as executor:
#     # 获取需要处理的文件列表
#     image_files = glob.glob(source_image_path)
#     # 处理文件列表，但通过Process Pool划分工作，使用全部CPU！
#     for image_file, thumbnail_file in zip(image_files, executor.map(make_image_thumbnail, image_files)):
#         print(f"success end")

```
