#coding: utf-8

import os
import subprocess;

dir='d:/wma'
cmd='D:/Programs/ffmpeg/ffmpeg'

os.chdir(dir)
print(os.getcwd())

files=os.listdir(dir)

for x in files:
	mp3_file=x[0:x.rindex('.')]+'.mp3'
	exec_result=subprocess.call([cmd,  '-i', x, mp3_file])
	print(x, 'exec_result', exec_result)
