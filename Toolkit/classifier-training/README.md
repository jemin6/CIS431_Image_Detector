## User Guide for classifier-training:

### Environment Requirement:
* C++
* OpenCV
* TBB
* OpenMpi

### Build/Install:
Training Classifier

	1) generate one set of positive image, one set of negative image
	(positive image: images contain the object that you want to detect)	
	(negative image: images CANNOT contain the object you want to detect)

	2) put all positive images in the folder called pos, and all negative images in a folder called neg

	3) clone this repository into the your local machine, same directory as the pos/neg folders

	4) the ‘image_tbb.cpp’ file contains what you need for image modification

	5) create one new folder called ‘pos-training’ and another new folder called ‘neg-training’

	6) open up CMD

	7) type the following commands:

	g++ -I/opt/X11/include -o image_t bb image_tbb.cpp -L/opt/X11/lib -lX11 -ljpeg -ltbb `pkg-config --cflags --libs opencv` -std=c++11

	./image_tbb pos neg
	(this would execute the file to do the image modification. The `pkg-config --cflags --libs opencv` should be the directory of where you download the OpenCV package. )

	ls pos_training/*.jpg > pos.txt

	sed 's/$/ 1 0 0 100 100/' pos.txt > pos.txt
	
	opencv_createsample -info pos.txt -num #1 -w 45 -h 45 -vec training.vec
	(replace the #1 with the number of positive source image you have)
	
	ls neg_training/*.jpg > neg.txt
	
	mkdir data
	
	opencv_traincascade -data data -vec training.vec -bg neg.txt -numPos #1 -numNeg #2 -numStages #3 -w 45 -h 45 -featureType #4 -minHitRate 0.999 -maxFalseAlarmRate 0.5

	
	replace the following arguments:
	#1: number of positive number for each stage, this number must be less than the number of positive image you have;
	#2: number of negative images;
	#3: number of stage you want to train, for a simple object detection, it can go from 10 -20 stages;
	#4: featureType: LBP or HAAR, LBP has faster training time, lower accuracy. HAAR has longer training time, higher accuracy;

	8) You are done! After the training is finished, you should have your training classifier as a cascade.xml file. The training time may takes anywhere from minutes to days. 
