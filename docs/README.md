# BasicComp
> 记录-----面试计算机基础知识


--------

## 邓大总结的面试技巧与话术
### 为什么使用A
1. A的特点
2. 类似B,C,D的特点（挑重点）
3. 我的业务，场景特点
4. 所以选A
5. 目前效果（可选）
6. 改进方向（可选）

### 你如何解决了X
1. 业界有A,B,C三种方案（重点比较）
2. 我的业务，场景特点
3. 所以用A解决了X
4. 方案缺陷(可选)
5. 改进方向（可选）

如果是内推的话，对面用的如果和你一样的技术，你可以讲的深入一点。

### 究竟和面试官聊什么
按照图中标注的数字，数字越小优先级越高
![N6M04z](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/N6M04z.png)

### 小技巧：如果面试官问到，你了解XXXX么
面试官出发点：
1. 你的简历上有写
2. 公司里面用到了XXX
回答方式
第一种：回答的时候谦虚一点，比如有所了解，略有耳闻，知道一丢丢
第二种：如果真的了解过，不过对XXXXX的A B C稍微了解一点

比如会问你你对golang了解么，我们可以回答对golang了解，不过对GMP，GC稍微了解一点


### 小技巧：准备案例

优先使用奇诡的案例(面试官有兴趣)，然后使用经典案例


--------





# -- coding:utf-8 --
# @Name:    类和对象的使用.py
# @IDE:     PyCharm   
# @Author:  wuxiaowen
# @Time:    2021/4/22 8:34 上午
# @Desc:    说明


import os
import cv2
import json
import numpy as np

# 全局定义原图存放的路径
image_path = '.'
# 全局定于卡通图存放的路径
cartoon_image_path = ''
# 原图的结果序列化到的文件
origin_serialize_file = './original_entropy.json'
# 卡通的结果序列化到的文件
cartoon_serialize_file = './cartoon_entropy.json'


class ImageObject:
    def __init__(self, image_name):
        # 图片的名字
        self.image_name = image_name
        # 一维的灰度熵
        self.one_dimen_gray_entropy = 0
        # 二维的灰度熵
        self.two_dimen_gray_entropy = 0

        # 一维的HSV各个颜色通道的熵
        self.one_dimen_HSV_H_channel_entropy = 0
        self.one_dimen_HSV_S_channel_entropy = 0
        self.one_dimen_HSV_V_channel_entropy = 0
        # 一维的HLS各个颜色通道的熵
        self.one_dimen_HLS_H_channel_entropy = 0
        self.one_dimen_HLS_L_channel_entropy = 0
        self.one_dimen_HLS_S_channel_entropy = 0
        # 一维的YUV各个颜色通道的熵
        self.one_dimen_YUV_Y_channel_entropy = 0
        self.one_dimen_YUV_U_channel_entropy = 0
        self.one_dimen_YUV_V_channel_entropy = 0
        # 一维的YCrCb各个颜色通道的熵
        self.one_dimen_YCrCb_Y_channel_entropy = 0
        self.one_dimen_YCrCb_Cr_channel_entropy = 0
        self.one_dimen_YCrCb_Cb_channel_entropy = 0

        # 二维的HSV各个颜色通道的熵
        self.two_dimen_HSV_H_channel_entropy = 0
        self.two_dimen_HSV_S_channel_entropy = 0
        self.two_dimen_HSV_V_channel_entropy = 0
        # 二维的HLS各个颜色通道的熵
        self.two_dimen_HLS_H_channel_entropy = 0
        self.two_dimen_HLS_L_channel_entropy = 0
        self.two_dimen_HLS_S_channel_entropy = 0
        # 二维的YUV各个颜色通道的熵
        self.two_dimen_YUV_Y_channel_entropy = 0
        self.two_dimen_YUV_U_channel_entropy = 0
        self.two_dimen_YUV_V_channel_entropy = 0
        # 二维的YCrCb各个颜色通道的熵
        self.two_dimen_YCrCb_Y_channel_entropy = 0
        self.two_dimen_YCrCb_Cr_channel_entropy = 0
        self.two_dimen_YCrCb_Cb_channel_entropy = 0

    def get_entropy(self):
        # 首先打开图片然后将图片大小改变到256*256
        # todo:这里会因为颜色通道的问题出错，因为有的时候是3个颜色通道，后面我们是一个二维的数组
        image = cv2.imread(image_path + os.sep + self.image_name)
        image_resize = cv2.resize(image, (256, 256))
        # 图片转换为灰度
        img_GRAY = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
        # 图片转换为HSV颜色空间
        img_HSV = cv2.cvtColor(image, cv2.COLOR_BGR2HSV)
        # 图片转换为HLS颜色空间
        img_HLS = cv2.cvtColor(image, cv2.COLOR_BGR2HLS)
        # 图片转换为YUV颜色空间
        img_YUV = cv2.cvtColor(image, cv2.COLOR_BGR2YUV)
        # 图片转换为YCrCb颜色空间
        img_YCrCb = cv2.cvtColor(image, cv2.COLOR_BGR2YCR_CB)

        # 一维的灰度熵
        self.one_dimen_gray_entropy = float(self.calc_entropy_gray_image(img_GRAY))
        # 二维的灰度熵
        self.two_dimen_gray_entropy = float(self.calc_entropy_two_dims_gray(img_GRAY))

        # 一维的HSV各个颜色通道的熵
        ret = self.calc_image_entropy_one_dim_color_space_entropy(img_HSV, "HSV")
        self.one_dimen_HSV_H_channel_entropy = float(ret[0])
        self.one_dimen_HSV_S_channel_entropy = float(ret[1])
        self.one_dimen_HSV_V_channel_entropy = float(ret[2])
        # 一维的HLS各个颜色通道的熵
        ret = self.calc_image_entropy_one_dim_color_space_entropy(img_HLS, "HLS")
        self.one_dimen_HLS_H_channel_entropy = float(ret[0])
        self.one_dimen_HLS_L_channel_entropy = float(ret[1])
        self.one_dimen_HLS_S_channel_entropy = float(ret[2])
        # 一维的YUV各个颜色通道的熵
        ret = self.calc_image_entropy_one_dim_color_space_entropy(img_YUV, "YUV")
        self.one_dimen_YUV_Y_channel_entropy = float(ret[0])
        self.one_dimen_YUV_U_channel_entropy = float(ret[1])
        self.one_dimen_YUV_V_channel_entropy = float(ret[2])
        # 一维的YCrCb各个颜色通道的熵
        ret = self.calc_image_entropy_one_dim_color_space_entropy(img_YCrCb, "YCrCb")
        self.one_dimen_YCrCb_Y_channel_entropy = float(ret[0])
        self.one_dimen_YCrCb_Cr_channel_entropy = float(ret[1])
        self.one_dimen_YCrCb_Cb_channel_entropy = float(ret[2])

        # 二维的HSV各个颜色通道的熵
        ret = self.calc_entropy_two_dims_color_space_entropy(img_HSV, "HSV")
        self.two_dimen_HSV_H_channel_entropy = float(ret[0])
        self.two_dimen_HSV_S_channel_entropy = float(ret[1])
        self.two_dimen_HSV_V_channel_entropy = float(ret[2])
        # 二维的HLS各个颜色通道的熵
        ret = self.calc_entropy_two_dims_color_space_entropy(img_HLS, "HLS")
        self.two_dimen_HLS_H_channel_entropy = float(ret[0])
        self.two_dimen_HLS_L_channel_entropy = float(ret[1])
        self.two_dimen_HLS_S_channel_entropy = float(ret[2])
        # 二维的YUV各个颜色通道的熵
        ret = self.calc_entropy_two_dims_color_space_entropy(img_YUV, "YUV")
        self.two_dimen_YUV_Y_channel_entropy = float(ret[0])
        self.two_dimen_YUV_U_channel_entropy = float(ret[1])
        self.two_dimen_YUV_V_channel_entropy = float(ret[2])
        # 二维的YCrCb各个颜色通道的熵
        ret = self.calc_entropy_two_dims_color_space_entropy(img_YCrCb, "YCrCb")
        self.two_dimen_YCrCb_Y_channel_entropy = float(ret[0])
        self.two_dimen_YCrCb_Cr_channel_entropy = float(ret[1])
        self.two_dimen_YCrCb_Cb_channel_entropy = float(ret[2])

    def calc_entropy_gray_image(self, image):
        """
        计算灰度图的熵，使用最简单的熵计算公式
        :param image: 使用opencv读取的灰度图
        :return:
        """
        hist = cv2.calcHist([image], [0], None, [256], [0, 256])
        hist = hist[hist > 0]
        probablity = hist / np.sum(hist)
        print('sum of probability:', np.sum(probablity))
        result = -np.matmul(probablity.T, np.log2(probablity))
        print("##################################成功计算%s的一维灰度熵：%f##################################" % (
            self.image_name, result))
        return -result

    # 计算各个颜色空间下，各个通道的整体的熵
    def calc_image_entropy_one_dim_color_space_entropy(self, image, color_space):
        """
        :param image: 使用opencv读取到的HSV颜色空间的图
        :return:
        """
        size = image.shape
        result = []
        for i in range(size[len(size) - 1]):
            hist = cv2.calcHist([image], [i], None, [256], [0, 256])
            hist = hist[hist > 0]
            # print(hist)
            probablity = hist / np.sum(hist)
            print('sum of probability:', np.sum(probablity))
            result.append(-(np.matmul(probablity.T, np.log2(probablity))))
        print("##################################成功计算%s在%s颜色空间下的一维熵：%s##################################" % (
            self.image_name, color_space, result))
        return result

    # 改进，采用字典统计次数然后计算我们的熵
    def calc_entropy_two_dims_gray(self, image, area=3):
        """
        计算图像空间二维熵：https://blog.csdn.net/marleylee/article/details/78813630
            p(i, j) = f(i, j) / N*N  其中i表示像素的灰度值(0 <= i <= 255)，j 表示邻域灰度均值(0 <= j <= 255)
        上式能反应某像素位置上的灰度值与其周围像素灰度分布的综合特征，其中f(i, j)为特征二元组(i, j)出现的频数，N 为图像的尺度。
            H = -sum_i(sum_j(p(i, j) * log2 p(i, j)))
        :param image: 要计算熵的图像
        :param area: 空间区域
        :return: 返回计算好的熵
        """
        original_height, original_width = image.shape
        # 首行增加0
        image_gray = np.insert(image, 0, values=0.0, axis=0)
        # 首列增加0
        image_gray = np.insert(image_gray, 0, values=0.0, axis=1)
        # 参考：https://blog.csdn.net/IMWTJ123/article/details/103124169
        # 最后一行加全0
        image_gray = np.row_stack((image_gray, np.zeros([original_width + 1])))
        # 最后一列加全0
        image_gray = np.column_stack((image_gray, np.zeros([original_height + 2])))

        # 计算f(i, j)
        kernel = np.ones((area, area), np.float32) / (area ** 2)
        # 参考：https://www.cnblogs.com/lfri/p/10599420.html
        dst_image = cv2.filter2D(image_gray, -1, kernel)

        # 根据均值滤波器计算得到的像素均值
        dst_image = dst_image[1:-1, 1:-1]

        # 计算均值滤波器
        height, width = image_gray.shape

        # 统计二元组出现的次数
        count = dict()

        for i in range(height - 2):
            for j in range(width - 2):
                # 存储均值滤波器中心点位于原图的像素
                f_i = image_gray[i + 1, j + 1]
                # 存储均值滤波器计算时候的均值像素
                f_j = dst_image[i, j]
                if (f_i, f_j) in count:
                    count[(f_i, f_j)] += 1
                else:
                    count[(f_i, f_j)] = 1

        probability = []
        for key, value in count.items():
            probability.append(value)
        print(probability)
        probability = np.array(probability, np.float32) / (original_width * original_height)
        print("概率之和：", np.sum(probability))
        # 根据计算出的概率和熵公式计算熵
        result = -np.matmul(probability, np.log2(probability).T)
        print("##################################成功计算%s在灰度颜色空间下的二维熵：%s##################################" %
              (self.image_name, result))
        return result

    # 改进，采用字典统计次数然后计算我们的熵
    # 计算三个通道的颜色空间下的各个通道的二维熵
    def calc_entropy_two_dims_color_space_entropy(self, image, color_space, area=3):
        """
        计算图像空间二维熵：https://blog.csdn.net/marleylee/article/details/78813630
            p(i, j) = f(i, j) / N*N  其中i表示该通道下的值(0 <= i <= 255)，j 表示邻域内该通道下均值(0 <= j <= 255)
        上式能反应某像素位置上的值与其周围像素在该通道下分布的综合特征，其中f(i, j)为特征二元组(i, j)出现的频数，N 为图像的尺度。
            H = -sum_i(sum_j(p(i, j) * log2 p(i, j)))
        :param image: 要计算熵的图像
        :param area: 空间区域
        :return: 返回计算好的熵
        """
        # 获取图片的shape
        size = image.shape
        # 获取输入数据的宽度以及高度
        original_height, original_width = size[0], size[1]

        # 我们最后要放置3个通道的熵到该结果中
        result = []
        for i in range(len(size)):  # 计算每个通道下面图像的熵
            image_data = image[:, :, i]  # 只是在第3个维度上取根据不同颜色通道选取通道下的值
            # 首行增加0
            image_data = np.insert(image_data, 0, values=0.0, axis=0)
            # 首列增加0
            image_data = np.insert(image_data, 0, values=0.0, axis=1)
            # 参考：https://blog.csdn.net/IMWTJ123/article/details/103124169
            # 最后一行加全0
            image_data = np.row_stack((image_data, np.zeros([original_width + 1])))
            # 最后一列加全0
            image_data = np.column_stack((image_data, np.zeros([original_height + 2])))

            # 计算f(i, j)
            kernel = np.ones((area, area), np.float32) / (area ** 2)
            # 参考：https://www.cnblogs.com/lfri/p/10599420.html
            dst_image = cv2.filter2D(image_data, -1, kernel)

            # 根据均值滤波器计算得到的像素均值
            dst_image = dst_image[1:-1, 1:-1]

            # 统计二元组出现的次数
            count = dict()

            for i in range(original_height - 2):
                for j in range(original_width - 2):
                    # 存储均值滤波器中心点位于原图的像素
                    f_i = image_data[i + 1, j + 1]
                    # 存储均值滤波器计算时候的均值像素
                    f_j = dst_image[i, j]
                    if (f_i, f_j) in count:
                        count[(f_i, f_j)] += 1
                    else:
                        count[(f_i, f_j)] = 1
            probability = []
            for key, value in count.items():
                probability.append(value)
            probability = np.array(probability, np.float32) / (original_width * original_height)
            print("概率之和：", np.sum(probability))
            # 根据计算出的概率和熵公式计算熵
            H = -np.matmul(probability, np.log2(probability).T)
            result.append(H)
        print("##################################成功计算%s在%s颜色空间下的二维熵：%s##################################" % (
            self.image_name, color_space, result))
        return result


def calc_entropy_two_dims_HSV(image, area=3):
    """
    计算图像空间二维熵：https://blog.csdn.net/marleylee/article/details/78813630
        p(i, j) = f(i, j) / N*N  其中i表示该通道下的值(0 <= i <= 255)，j 表示邻域内该通道下均值(0 <= j <= 255)
    上式能反应某像素位置上的值与其周围像素在该通道下分布的综合特征，其中f(i, j)为特征二元组(i, j)出现的频数，N 为图像的尺度。
        H = -sum_i(sum_j(p(i, j) * log2 p(i, j)))
    :param image: 要计算熵的图像
    :param area: 空间区域
    :return: 返回计算好的熵
    """
    # 获取图片的shape
    size = image.shape
    # 获取输入数据的宽度以及高度
    original_height, original_width = size[0], size[1]

    # 我们最后要放置3个通道的熵到该结果中
    result = []
    for i in range(len(size)):  # 计算每个通道下面图像的熵
        image_data = image[:, :, i]  # 只是在第3个维度上取根据不同颜色通道选取通道下的值
        # 首行增加0
        image_data = np.insert(image_data, 0, values=0.0, axis=0)
        # 首列增加0
        image_data = np.insert(image_data, 0, values=0.0, axis=1)
        # 参考：https://blog.csdn.net/IMWTJ123/article/details/103124169
        # 最后一行加全0
        image_data = np.row_stack((image_data, np.zeros([original_width + 1])))
        # 最后一列加全0
        image_data = np.column_stack((image_data, np.zeros([original_height + 2])))

        # 计算f(i, j)
        kernel = np.ones((area, area), np.float32) / (area ** 2)
        # 参考：https://www.cnblogs.com/lfri/p/10599420.html
        dst_image = cv2.filter2D(image_data, -1, kernel)

        # 根据均值滤波器计算得到的像素均值
        dst_image = dst_image[1:-1, 1:-1]

        # 统计二元组出现的次数
        count = dict()

        for i in range(original_height - 2):
            for j in range(original_width - 2):
                # 存储均值滤波器中心点位于原图的像素
                f_i = image_data[i + 1, j + 1]
                # 存储均值滤波器计算时候的均值像素
                f_j = dst_image[i, j]
                if (f_i, f_j) in count:
                    count[(f_i, f_j)] += 1
                else:
                    count[(f_i, f_j)] = 1
        probability = []
        for key, value in count.items():
            probability.append(value)
        probability = np.array(probability, np.float32) / (original_width * original_height)
        print("概率之和：", np.sum(probability))
        # 根据计算出的概率和熵公式计算熵
        H = -np.matmul(probability, np.log2(probability).T)
        result.append(H)
        print("熵:", H)
    return result


class ImageObj2Json(json.JSONEncoder):
    def default(self, img: ImageObject):
        # 返回字典类型
        return {
            "image_name": img.image_name,
            "one_dimen_gray_entropy": float(img.one_dimen_gray_entropy),
            "two_dimen_gray_entropy": img.two_dimen_gray_entropy,
            "one_dimen_HSV_H_channel_entropy": img.one_dimen_HSV_H_channel_entropy,
            "one_dimen_HSV_S_channel_entropy": img.one_dimen_HSV_S_channel_entropy,
            "one_dimen_HSV_V_channel_entropy": img.one_dimen_HSV_V_channel_entropy,
            "one_dimen_HLS_H_channel_entropy": img.one_dimen_HLS_H_channel_entropy,
            "one_dimen_HLS_L_channel_entropy": img.one_dimen_HLS_L_channel_entropy,
            "one_dimen_HLS_S_channel_entropy": img.one_dimen_HLS_S_channel_entropy,
            "one_dimen_YUV_Y_channel_entropy": img.one_dimen_YUV_Y_channel_entropy,
            "one_dimen_YUV_U_channel_entropy": img.one_dimen_YUV_U_channel_entropy,
            "one_dimen_YUV_V_channel_entropy": img.one_dimen_YUV_V_channel_entropy,
            "one_dimen_YCrCb_Y_channel_entropy": img.one_dimen_YCrCb_Y_channel_entropy,
            "one_dimen_YCrCb_Cr_channel_entropy": img.one_dimen_YCrCb_Cr_channel_entropy,
            "one_dimen_YCrCb_Cb_channel_entropy": img.one_dimen_YCrCb_Cb_channel_entropy,
            "two_dimen_HSV_H_channel_entropy": img.two_dimen_HSV_H_channel_entropy,
            "two_dimen_HSV_S_channel_entropy": img.two_dimen_HSV_S_channel_entropy,
            "two_dimen_HSV_V_channel_entropy": img.two_dimen_HSV_V_channel_entropy,
            "two_dimen_HLS_H_channel_entropy": img.two_dimen_HLS_H_channel_entropy,
            "two_dimen_HLS_L_channel_entropy": img.two_dimen_HLS_L_channel_entropy,
            "two_dimen_HLS_S_channel_entropy": img.two_dimen_HLS_S_channel_entropy,
            "two_dimen_YUV_Y_channel_entropy": img.two_dimen_YUV_Y_channel_entropy,
            "two_dimen_YUV_U_channel_entropy": img.two_dimen_YUV_U_channel_entropy,
            "two_dimen_YUV_V_channel_entropy": img.two_dimen_YUV_V_channel_entropy,
            "two_dimen_YCrCb_Y_channel_entropy": img.two_dimen_YCrCb_Y_channel_entropy,
            "two_dimen_YCrCb_Cr_channel_entropy": img.two_dimen_YCrCb_Cr_channel_entropy,
            "two_dimen_YCrCb_Cb_channel_entropy": img.two_dimen_YCrCb_Cb_channel_entropy
        }


def serialize(image_dir_path, serialize_ret_path):
    """
    :param image_dir_path: 图片所在的目录
    :param serialize_ret_path: 序列化到的目录以及文件完整路径，比如'./ret.txt'
    :return:
    """
    # 定义一个结果保存我们最终序列化的结果
    ret = []
    # 从我们全局定义的原图路径中遍历所有的图
    for img_name in os.listdir(image_dir_path):
        # 读取图片
        img = ImageObject(img_name)
        # 计算第一张图片的属性
        img.get_entropy()
        # 序列化到一个对象中
        image2Obj = json.dumps(img, cls=ImageObj2Json)
        # 加入到结果中
        ret.append(image2Obj)

    # 最后将我们的结果序列化
    ret = json.dumps(ret)
    # 将我们序列化之后的结果保存到一个文件中
    with open(serialize_ret_path, 'w') as f:
        f.write(ret)


if __name__ == '__main__':
    # 计算原图的结果并保存到文件中
    serialize(image_path, origin_serialize_file)
    # 计算卡通图的结果并保存到文件中
    serialize(cartoon_image_path, cartoon_serialize_file)

    # 之后如果要使用的话，直接加载
    # 从文件中读取我们序列化的结果
    # with open(origin_serialize_file, 'r') as f:
    #     origin_data = json.loads(f.read())
    #
    # with open(cartoon_serialize_file, 'r') as f:
    #     cartoon_data = json.loads(f.read())



# if __name__ == '__main__':
#     ret = []
#     # 读取第一张图片
#     img = ImageObject("29994.jpg")
#     # 计算第一张图片的属性
#     img.get_entropy()
#     # 序列化到一个对象中
#     image2Obj = json.dumps(img, cls=ImageObj2Json)
#     # 加入到结果中
#     ret.append(image2Obj)
#
#     # 读取第2张图片
#     img = ImageObject("29123.jpg")
#     # 计算第二张图片的属性
#     img.get_entropy()
#     # 序列化到一个对象中
#     image2Obj2 = json.dumps(img, cls=ImageObj2Json)
#     # 加入到结果中
#     ret.append(image2Obj2)
#
#     # 最后将我们的结果序列化
#     ret = json.dumps(ret)
#     # 将我们序列化之后的结果保存到一个文件中
#     with open('image.json', 'w') as f:
#         f.write(ret)
#
#     # 从文件中读取我们序列化的结果
#     with open('image.json', 'r') as f:
#         data = json.loads(f.read())
#     print(data)

# ['{"image_name": "29994.jpg", "one_dimen_gray_entropy": 7.188683032989502, "two_dimen_gray_entropy": 11.481466984190456, "one_dimen_HSV_H_channel_entropy": 4.023153781890869, "one_dimen_HSV_S_channel_entropy": 6.671289443969727, "one_dimen_HSV_V_channel_entropy": 7.410058975219727, "one_dimen_HLS_H_channel_entropy": 4.024173736572266, "one_dimen_HLS_L_channel_entropy": 7.157116889953613, "one_dimen_HLS_S_channel_entropy": 6.868276119232178, "one_dimen_YUV_Y_channel_entropy": 7.188683032989502, "one_dimen_YUV_U_channel_entropy": 4.318329811096191, "one_dimen_YUV_V_channel_entropy": 4.758913993835449, "one_dimen_YCrCb_Y_channel_entropy": 7.188683032989502, "one_dimen_YCrCb_Cr_channel_entropy": 4.833278656005859, "one_dimen_YCrCb_Cb_channel_entropy": 4.589835166931152, "two_dimen_HSV_H_channel_entropy": 6.341136655405334, "two_dimen_HSV_S_channel_entropy": 11.862393371088599, "two_dimen_HSV_V_channel_entropy": 11.794230183128198, "two_dimen_HLS_H_channel_entropy": 6.346707115635988, "two_dimen_HLS_L_channel_entropy": 11.506117788904897, "two_dimen_HLS_S_channel_entropy": 12.490129585857574, "two_dimen_YUV_Y_channel_entropy": 11.430127246999884, "two_dimen_YUV_U_channel_entropy": 5.936443588739264, "two_dimen_YUV_V_channel_entropy": 6.632888128465668, "two_dimen_YCrCb_Y_channel_entropy": 11.430127246999884, "two_dimen_YCrCb_Cr_channel_entropy": 6.6411284469363885, "two_dimen_YCrCb_Cb_channel_entropy": 6.329502150071099}',
# '{"image_name": "29123.jpg", "one_dimen_gray_entropy": 7.675539016723633, "two_dimen_gray_entropy": 12.592793066750938, "one_dimen_HSV_H_channel_entropy": 3.9510300159454346, "one_dimen_HSV_S_channel_entropy": 6.8779497146606445, "one_dimen_HSV_V_channel_entropy": 7.639270782470703, "one_dimen_HLS_H_channel_entropy": 3.9544599056243896, "one_dimen_HLS_L_channel_entropy": 7.6025519371032715, "one_dimen_HLS_S_channel_entropy": 7.1482954025268555, "one_dimen_YUV_Y_channel_entropy": 7.675539016723633, "one_dimen_YUV_U_channel_entropy": 3.8567147254943848, "one_dimen_YUV_V_channel_entropy": 4.1637187004089355, "one_dimen_YCrCb_Y_channel_entropy": 7.675539016723633, "one_dimen_YCrCb_Cr_channel_entropy": 4.255394458770752, "one_dimen_YCrCb_Cb_channel_entropy": 4.150216102600098, "two_dimen_HSV_H_channel_entropy": 6.500574994607927, "two_dimen_HSV_S_channel_entropy": 12.385685909225915, "two_dimen_HSV_V_channel_entropy": 12.59973213015977, "two_dimen_HLS_H_channel_entropy": 6.515144192985374, "two_dimen_HLS_L_channel_entropy": 12.5306020218659, "two_dimen_HLS_S_channel_entropy": 12.954833287092116, "two_dimen_YUV_Y_channel_entropy": 12.534537443398607, "two_dimen_YUV_U_channel_entropy": 5.450361518264728, "two_dimen_YUV_V_channel_entropy": 5.911838589281736, "two_dimen_YCrCb_Y_channel_entropy": 12.534537443398607, "two_dimen_YCrCb_Cr_channel_entropy": 5.962947175972769, "two_dimen_YCrCb_Cb_channel_entropy": 5.869834640168771}']


# if __name__ == '__main__':
#     image_name = "29994.jpg"
#     # hist = cv2.calcHist([image], [0], None, [256], [0, 256])
#     # hist = hist[hist > 0]
#     # probablity = hist / np.sum(hist)
#     # print('sum of probability:', np.sum(probablity))
#     # result = np.matmul(probablity.T, np.log2(probablity))
#     # print(type(result))
#     # print(result)
#     # result = -np.sum(result)
#     # # print("##################################成功计算%s的一维灰度熵：%f##################################" % (
#     # #     self.image_name, -result))
#     # print(result)
#
#     image = cv2.cvtColor(cv2.imread(image_name), cv2.COLOR_BGR2HSV)
#     size = image.shape
#     result = []
#     for i in range(size[len(size) - 1]):
#         hist = cv2.calcHist([image], [i], None, [256], [0, 256])
#         hist = hist[hist > 0]
#         # print(hist)
#         probablity = hist / np.sum(hist)
#         print(probablity.shape)
#         print(probablity)
#         print(probablity.T.shape)
#         print('sum of probability:', np.sum(probablity))
#         print("1：", -(np.matmul(probablity.T, np.log2(probablity))))
#         result.append(-(np.matmul(probablity.T, np.log2(probablity))))
#         print("2：", - np.sum(np.matmul(probablity.T, np.log2(probablity))))
#
#     data = np.array([2, 2, 2])
#     print(np.log2(data))
#     print(np.matmul(data.T, np.log2(data)))
#     print(np.e)
#     print(np.array(data).shape)
#     print(np.matmul(probablity.T, np.log2(probablity)))
