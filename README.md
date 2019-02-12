1. ls neg/*.jpg > neg.txt
	���������·��
2. ls pos/*.jpg > pos.txt
	��pos.txt�е�.jpg ȫ���滻�� .jpg 1 0 0 24 24
	
3. ����vec�ļ�
	 opencv_createsamples -info pos.txt -vec pos.vec -bg neg.txt -num 2000 -w 24 -h 24
	 ���� -num 2000 ��ʾ����2000����
	 
4. ѵ��
opencv_traincascade -data xml -vec pos.vec -bg neg.txt -numPos 2000 -numNeg 4000 -numStages 20 -precalcValBufSize 2048 -precalcIdxBufSize 2048 -numThreads 8 -featureType LBP -w 24 -h 24

���������߳�����mem
     ��ʵ���ѵ��������;ֹͣ�ģ���Ϊ�´ο���ʱ�����ȡ��Щxml�ļ������Ž����ϴ�δ��ɵ�ѵ����
	 
5. cascade.detectMultiScale(gray, objects, 1.1, 3, CASCADE_FIND_BIGGEST_OBJECT, Size(10, 10), Size(40, 40));
         ��Ҫ����ʹ�õ���������С����ȷ��Size��С�� 
		 

		 
6.���Է�����


CMakeLists.txt



cmake_minimum_required( VERSION 2.8 )

project( test_cascade )

add_executable( ${PROJECT_NAME} main.cpp )

find_package( OpenCV REQUIRED )

target_link_libraries( ${PROJECT_NAME} ${OpenCV_LIBS} )



main.cpp


#include <opencv2/opencv.hpp>
#include <iostream>
#include <vector>
#include <stdio.h>

using namespace cv;
using namespace std;

int main(int argc, char *argv[])
{
  if(argc != 3)
  {
  printf("%s <cascade file> <image>\n", argv[1]);
  }

  CascadeClassifier cascade;
  if(!cascade.load(argv[1]))
  {
  cout << "cascade load failed." << endl;
  return -1;
  }

  Mat image, gray;

  image = imread(argv[2], 1);

  if(!image.empty())
  {
  cvtColor(image, gray, CV_BGR2GRAY);
  vector<Rect> objects;
  cascade.detectMultiScale(gray, objects, 1.1, 3, CASCADE_FIND_BIGGEST_OBJECT, Size(10, 10), Size(40, 40));
  if(objects.size() == 1)
  rectangle(image, objects[0], Scalar(0,0,255));
  imwrite("image.bmp", image);
  cout << "finished." << endl;
  }
  else
  {
  cout << "no image" << endl;
  }
  return 0;
}