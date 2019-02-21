# hello-start
# coding:utf-8
"""
@project = python-project
@file = register
@author = wangxu5
@create_time = 2018/9/13 15:30
部门内部自研人脸注册算法--张尧、陈佳斌
"""

import os
import time

import requests

import BJG_Business
from Methods import img_to_base64
from Methods import log

logger = log.logger()


# 人脸注册方法
def register(url, img):
    img_name = img.split('\\')[-1].split('.')[0]
    payload = {
        'base64': img_to_base64.img_to_bs4_new(img),
        'accountId': img_name,
        'blur': 0
    }
    headers = {
        'appId': 'QwerTest',
        'key': '123456'
    }
    try:
        response = requests.post(url=url, data=payload, headers=headers)
        if response.ok:
            if response.json()['code'] == '0000':
                logger.logger.info('img:{}; register success!'.format(img_name))
            else:
                if response.json()['code'] == 'S4004':
                    logger.logger.info('img:{}:{}'.format(img_name, '注册失败，当前APPID下该accountId已注册'))
                else:
                    logger.logger.info('img:{}; errorCode:{}'.format(img_name, response.json()['code']))
                    # shutil.move(img, 'E:\\Project\\BeiJiGuang\\bjg_business\\img_register_error\\')
        else:
            logger.logger.error('接口异常：{}'.format(response.status_code))
    except Exception as e:
        logger.logger.error('连接异常：{}'.format(e))


# 依次轮询文件夹内的图片
def do_test(url, folder_path):
    num = 1
    for img_name in os.listdir(folder_path):
        register(url, folder_path + img_name)
        time.sleep(5)
        logger.logger.info(num)
        num += 1


if __name__ == '__main__':
    URL = BJG_Business.IP + ':8080/face/register'
    IMG_PATH = r'E:\Project\BeiJiGuang\BJG_Business\img_blurring5_0.4\1531463027495.jpg'
    # register(url=URL, img=IMG_PATH)

    REGISTER_PATH = 'E:\\Project\\BeiJiGuang\\bjg_business\\img_register\\'
    do_test(url=URL, folder_path=REGISTER_PATH)
    # while True:
    #     do_test(url=URL, folder_path=REGISTER_PATH)

# 本地环境 外网IP
# 10.13.50.20
