#coding: utf-8

import os
import subprocess;

# split files based on m3u8 index file
def split_file(dir, m3u8):
	file=open(m3u8, encoding='UTF-8')

	seg_map=dict()

	try:
		while True:
			line=file.readline()
			if line:
				line=line.strip('\n')
				if line.startswith("#EXT-X-BYTERANGE"):
					s=line.split(":")[1].strip().split("@")
					seg=[]
					seg.append(int(s[0]))
					seg.append(int(s[1]))
					
					line=file.readline().strip()
					if line.find("/") >= 0:
						line=line[line.rindex("/")+1:]
						
					if line in seg_map:
						segs=seg_map[line]
					else:
						segs=[]
						seg_map[line]=segs
						
					segs.append(seg)
			else:
				print("file EOF:", m3u8)
				break
	finally:
		file.close()
		
	for key, value in seg_map.items():
		# print(key, value)
		f=open(dir+"/"+key, 'rb')
		try:
			for i in range(len(value)):
				seg=value[i]
				f.seek(seg[1])
				
				dst=open(dir+"/"+key+"_"+str(i)+".ts", 'wb')
				read_len=0
				next_len=1024
				try:
					while next_len > 0:
						chunk = f.read(next_len)
						if chunk:
							read_len+=next_len
							dst.write(chunk)
							if read_len+next_len>=seg[0]:
								next_len=seg[0]-read_len
						else:
							break
				finally:
					dst.close()
				
		finally:
			f.close()

	return 0

def c(s):
	s=s.split('.')[0]
	a=s.split('_')
	score=100*float(a[1])+float(a[2])
	return score

# root dir
root='D:/大长今国韩双语版.2005/.%s'
# dirs=[root]
# dirs=['']

dirs=[]
start=9
end=40
r=range(start, end)
for i in r:
	j=i
	if i < 10:
		j='0%s' % (i)
	dirs.append(root % (j))


for dir in dirs:
	os.chdir(dir)
	print(os.getcwd())

	files=os.listdir(dir)
	m3u8=''
	for x in files:
		if x.endswith('m3u8'):
			m3u8=x
			break

	if m3u8=='':
		print('no m3u8 file found in dir:', dir)
		continue

	m3u8_file=dir+'/'+m3u8

	# java_exec='C:/Java/jdk1.8.0_112/bin/java'
	# split_result=subprocess.call([java_exec, '-cp', 'd:/', 'FileSegmentation', m3u8_file])
	split_result = split_file(dir, m3u8_file)
	if split_result!=0:
		print('failed to segment ts files in dir:', dir)
		continue

	files=os.listdir(dir)
	tss=[]
	for x in files:
		if x.endswith('.ts'):
			tss.append(x)

	tss.sort(key=c)

	dst_file=open('list.txt', 'w')

	for x in tss:
		dst_file.write('file %s%s' % (dir+'/'+x,'\r\n'))

	dst_file.close()

	# cmd='D:/Programs/ffmpeg/ffmpeg -f concat %s -c copy -absf aac_adtstoasc %s.mp4' % ('-i list.txt', dir[dir.rindex('/')+1:])

	# print(cmd)
	cmd='D:/Programs/ffmpeg/ffmpeg'

	mp4_file=dir[dir.rindex('/')+1:]+'.mp4'
	if mp4_file.index('.')==0:
		mp4_file=mp4_file[1:]
	mp4_file='../'+mp4_file
	# exec_result=os.execv(cmd,['ffmpeg','-f concat','-i list.txt','-c copy','-absf','aac_adtstoasc', mp4_file])
	exec_result=subprocess.call([cmd, '-f', 'concat', '-safe', '0', '-i', 'list.txt', '-c', 'copy', mp4_file])
	# exec_result=os.system('%s -f concat -i list.txt -c copy -absf aac_adtstoasc %s' % (cmd, mp4_file))
	print(dir, 'exec_result', exec_result)
