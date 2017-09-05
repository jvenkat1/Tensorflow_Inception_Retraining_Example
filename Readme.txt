Training inception on our Women in Computing dataset
Instructions for MAC

Step 1: Download docker for mac
https://docs.docker.com/docker-for-mac/install/#download-docker-for-mac

Step 2: 
Installing and Running the TensorFlow Docker Image
docker run -it gcr.io/tensorflow/tensorflow:latest-devel

To ensure you have tensorflow working successfully
Run python command
Once you see the python prompt run the following

import tensorflow as tf
hello = tf.constant('Hello, TensorFlow!')
sess = tf.Session()
print(sess.run(hello))

You should see “Hello, TensorFlow!” message


Step3: Download fatkun batch download extension for google chrome to save all search images easily into a folder. Create a folder named tf_files in any directory and all the images you would like to classify here. In this example we have add images for 5 different Women In Computing to our training set. 

Step 4: Virtually Linking images to the tensor flow docker terminal. I put the images downloaded for our women in computing dataset into the $HOME/Documents/ directory 
docker run -it -v $HOME/Documents/tf_files:/tf_files  gcr.io/tensorflow/tensorflow:latest-devel
You should see tf_files folder in the root directory inside the docker terminal. 
Next cd into /tensorflow directory and do a git pull to get latest tensor flow top of the tree code (docker image for tensorflow may not be the latest)

Step 4: 
Retrain the model: 
Inception is a huge image classification model with millions of parameters that can differentiate a large number of kinds of images. We're only training the final layer of that network, so training will end in a reasonable amount of time. In this example inception was not trained on the women in computing dataset but by using the pre-trained model we use all the earlier information available because inception has been trained on 1000 classes. We use this earlier information available as input to the final classification layer to distinguish our women in computing.  

python retrain.py \
--bottleneck_dir=/tf_files/bottlenecks \
--how_many_training_steps 500 \
--model_dir=/tf_files/inception \
--output_graph=/tf_files/retrained_graph.pb \
--output_labels=/tf_files/retrained_labels.txt \
--image_dir /tf_files/topWomenInComputing
The retraining script will write out a version of the Inception network with a final layer retrained to our categories to tf_files/output_graph.pb and a text file containing the labels to tf_files/output_labels.txt.


Step 5: 
Next we fetch the python script that loads our graph file and predicts with it. We copy this python script to our tf_files directory.
curl -L https://goo.gl/tx3dqg > $HOME/Documents/tf_files/label_image.py

Now we link tensor flow docker image to the label_image.py script virtually: 
docker run -it -v $HOME/Documents/tf_files:/tf_files  gcr.io/tensorflow/tensorflow:latest-devel 

Step 6: 
Now, run the Python file you created, first on the grace hopper image:
# In Docker
python /tf_files/label_image.py /tf_files/topWomenInComputing/admiral\ grace\ hopper/images\ \(22\).jpg

And then on Ursula Burns image: 
python /tf_files/label_image.py /tf_files/topWomenInComputing/ursula\ burns/images\ \(39\).jpg 

You might get results like this for a grace hopper photo:
admiral grace hopper (score = 0.98417)
ursula burns (score = 0.00535)
marissa mayer (score = 0.00414)
ginni rometty (score = 0.00340)
megan smith (score = 0.00294)
This indicates a high confidence that the image is of grace hopper and low confidence that image is any other label. 







