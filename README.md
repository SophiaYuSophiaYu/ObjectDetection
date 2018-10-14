# 1、环境准备
	本次作业使用的slim版本为r1.5，主要利用object_detection模块对图片中多个目标进行检测。
	object_detection模块使用前需要先进行安装，安装详细流程流程如下面网址所示：
https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/installation.md
首先，安装一些依赖项，包括tensorflow、Cython、pillow等；
然后，还需要对Protobuf进行编译，在tensorflow/models/research/目录下，运行命令
protoc object_detection/protos/*.proto --python_out=.
编译成功后，会利用proto文件编译生成对应py文件，如下图所示：     
        最后，进行环境变量设置，主要目的是使我们在运行object_detection代码时可以调用slim框架下定义的网络结构，具体操作是，在tensorflow/models/research/目录下，执行命令
export PYTHONPATH=$PYTHONPATH:`pwd`:`pwd`/slim
如果经常要使用这个环境变量，那么可以把此环境变量定义到~/.bashrc文件中去，具体操作为
编辑bashrc文件
gedit .bashrc
将下条命令添加到bashrc文件的最后
export PATH=$PATH:$HOME/sophiayu/tensorflow/models-r1.5/research/slim
测试是否安装成功，在tensorflow/models/research/目录下，执行命令
python object_detection/builders/model_builder_test.py
若安装成功，界面会显示“OK”。

# 2、生成trainval.txt文件
        在生成record文件时，我们需要用到trainval.txt文件，文件内容很简单，为训练图片文件的文件名，生成文件时使用的具体代码见CreatTrainval.ipynb，生成的trainval.txt文件如下图所示。
 

# 3、生成record数据文件
	以models官方代码create_pet_tf_record.py为基础进行修改，此代码具体路径为
tensorflow/models/research/object_detection/dataset_tools/create_pet_tf_record.py
该代码将xml文件中的图片尺寸信息和物体位置信息统一进行处理，物体位置会转换成0～1之间的比例，物体名字会根据label_map转换成一个数字列表，结果会存入tfrecord中。
	因为本次作业数据集没有mask，因此注释掉代码中的faces_only和mask内容。
	从main()函数开始修改，具体代码如下图所示。从命令行提供的路径读取所有图片examples_list，然后随机选取其中的70%作为训练集，30%作为测试集，最后调用create_tf_record()函数生成record文件。        
        create_tf_record()函数中，首先，打开一个TFRecordWriter，然后，遍历传入的所有exampls，读取其xml文件，最后，调用函数dict_to_tf_example()的到TFRecord数据，并写入生成文件中。
        dict_to_tf_example()函数主要对xml中的数据进行解析，包括定位框位置信息、图片大小等

# 4、修改run.sh及ssd_mobilenet_v1_quiz_w9.config文件
	将数据和配置文件上传至tinymind，并将代码上传至github，修改run.sh和ssd_mobilenet_v1_quiz_w9.config中相应的路径符合当前配置。

# 5、调试过程
        在代码调试过程中，出现过多个错误：
（1）	未导入protos生成的py文件，导致运行失败；
（2）	找不到modul，因为未上传slim文件夹；
（3）	
2018-10-13 18:17:55.582240: F tensorflow/core/kernels/conv_ops.cc:667] Check failed: stream->parent()->GetConvolveAlgorithms( conv_parameters.ShouldIncludeWinogradNonfusedAlgo(), &algorithms) 
./run.sh: line 30:    12 Aborted                 (core dumped) python ./object_detection/train.py --train_dir=$train_dir --pipeline_config_path=$pipeline_config_path
 检查发现修改exporter.py代码时有语法错误，少写一个“)”。

