# 论文阅读笔记
一些论文笔记，整理

## 目录
- [一. RNN](#一-rnn)
- [二. CNN](#二-cnn)
	- [经典网络结构](#1-经典网络结构)
	- [风格迁移](#2-风格迁移)
	- [Point Cloud](#3-Point-Cloud)
	- [目标检测](#4-目标检测)
	- [结构分析](#5-cnn结构分析)
	- [streamline](#6-提取流场streamline特征)
	- [涡识别](#7-识别流场vortex)
- [三. GAN](#三-gan)
	- [Volume render](#1-volume)
	- [图像生成](#2-gan图像生成)
  	- [3DGAN](#3-3dgan)
- [四. 超分辨率](#四-超分辨率)
	- [Volume&Isosurface](#1-volume-and-isosurface)
	- [Image SR](#2-image)
	- [Video SR](#3-video)
	- [Video interpolation](#4-Video-interpolation-Temporal-super-resolution)
	- [时序流场SR](#5-时序流场)
- [五. Deblur](#五-deblur)
	- [Image Deblur](#1-image-deblur)
	- [Video Deblur](#2-video-deblur)
- [六. 视频预测](#六-视频预测)
	- [GAN](#1-gan)
	- [CNN](#2-cnn)
- [七. NLP](#七-nlp)
	- [Attention](#1-attention)
	- [Transformer](#2-transformer)
	- [Memory机制](#3-memory)
- [八. HPC](#八-hpc)
	- [并行粒子追踪](#并行粒子追踪)
- [九. Flow visualization](#九-flow-visualization)
	- [FTLE](#1-ftle)
	- [流体模拟](#2-fluid-simulation)
	- [Streamline/Seed Selection](#3-streamline-selection)

### 一. RNN
- 流场可视化：预测粒子跟踪中的**数据访问**模式（LSTM）

  >*Hong, Fan & Zhang, Jiang & Yuan, Xiaoru. (2018). [Access Pattern Learning with Long Short-Term Memory for Parallel Particle Tracing.](http://vis.pku.edu.cn/research/publication/pacificvis18-dlprefetch.pdf) 76-85. 10.1109/PacificVis.2018.00018.*

- <span id="tsr">流场可视化：TSR-TVD：流场时序的高分辨率</span>
  >*Han, J., & Wang, C. (2019). [TSR-TVD: Temporal Super-Resolution for Time-Varying Data Analysis and Visualization ](https://www3.nd.edu/~cwang11/research/vis19-tsr.pdf). IEEE TVCG.*
  
	- 目标：对于两个时间步t,k之间的体数据，找到一个映射Φ，得到中间时间步的体数据
	- 网络结构RGN：RNN+GNN
  		- Generate：一个生成网络G(类似于bilstm的思想)
			1. 预测模型（biRNN）：前向预测（从t开始）和后向预测（从k开始）
			2. 融合模型：将前向后向的各个对应的时间步融合
		- Discriminator：一个判别模型D（二分类的CNN）
		
			区分出fake的体数据V
	- 3个loss funtion：（1）生成对抗loss（2）体数据和GroundTruth对比的损失（生成器尽可能的还原GT的体数据）（3）CNN中间层特征损失：		提取判别器D中的卷积层参数，使生成器生成的体数据在每一层都尽可能的贴近GroundTruth

- 流体可视化：预测流体**压力场p**的变化(RNN)
  >*Wiewel, S., Becher, M., & Thürey, N. (2018). [Latent Space Physics: Towards Learning the Temporal Evolution of Fluid Flow.](https://arxiv.org/abs/1802.10123) Comput. Graph. Forum, 38, 71-82.*
  
  由[TSR-TVD](#tsr)引用
  
  RNN预测3d物理函数的时间演变
  
  Navier-stokes方程：
  ![](https://cdn.mathpix.com/snip/images/CTOYuXYlH8iRbLGOmp50PTqIovm4q9IrrIGlzxtYiRo.original.fullsize.png)

  - Step 1:cnn对**p**的降维，autoencoder的模式，提取其中的latent vector作为特征向量**p**
  - Step 2:将序列**c**输入LSTM+CNN的混合架构（对于高维物理输出比较重要），输出未来h个时间步的预测值

- 预测内存存取模式，预取数据(LSTM)
  >*Hashemi, M., Swersky, K., Smith, J.A., Ayers, G., Litz, H., Chang, J., Kozyrakis, C.E., & Ranganathan, P. (2018). [Learning Memory Access Patterns](https://arxiv.org/pdf/1803.02329.pdf) ArXiv, abs/1803.02329.*
  
### 二. CNN
#### 1. 经典网络结构
- ResNet
	>*He, K., Zhang, X., Ren, S., & Sun, J. (2015). [Deep Residual Learning for Image Recognition. 2016 IEEE Conference on Computer Vision and Pattern Recognition (CVPR)](https://arxiv.org/abs/1512.03385)[(code)](https://github.com/pytorch/vision/blob/master/torchvision/models/resnet.py), 770-778.*

	详解：https://zhuanlan.zhihu.com/p/56961832

	https://blog.csdn.net/lanran2/article/details/79057994

	解决了什么问题：https://www.zhihu.com/question/64494691/answer/786270699

	其他变体：https://www.jiqizhixin.com/articles/042201

- ResNeXt
	>*Xie, S., Girshick, R.B., Dollár, P., Tu, Z., & He, K. (2016). [Aggregated Residual Transformations for Deep Neural Networks.](https://arxiv.org/abs/1611.05431) [(code)](https://github.com/Cadene/pretrained-models.pytorch/blob/master/pretrainedmodels/models/resnext.py) 2017 IEEE Conference on Computer Vision and Pattern Recognition (CVPR), 5987-5995.*

	详解：https://zhuanlan.zhihu.com/p/78019001

	https://blog.csdn.net/u014380165/article/details/71667916

- DenseNet
	>*Huang, G., Liu, Z., Maaten, L.V., & Weinberger, K.Q. (2016). [Densely Connected Convolutional Networks.](https://arxiv.org/abs/1608.06993) [(code)](https://github.com/liuzhuang13/DenseNet) 2017 IEEE Conference on Computer Vision and Pattern Recognition (CVPR), 2261-2269.*
	
	详解：https://blog.csdn.net/u014380165/article/details/75142664
	
#### 2. 风格迁移
- <span id="perceptual">Perceptual Loss:风格迁移</span>
	>*Johnson, J., Alahi, A., & Fei-Fei, L. (2016). [Perceptual Losses for Real-Time Style Transfer and Super-Resolution.](https://arxiv.org/abs/1603.08155) ECCV.*
- <span id="deepae">deep VAE学习图像语义特征</span>
	>*Hou, X., Shen, L., Sun, K., & Qiu, G. (2016). [Deep Feature Consistent Variational Autoencoder.](https://arxiv.org/abs/1610.00291) 2017 IEEE Winter Conference on Applications of Computer Vision (WACV), 1133-1141.*
#### 3. Point Cloud
Awesome Point Cloud：https://github.com/Yochengliu/awesome-point-cloud-analysis
- 点云分类
	>*Roveri, R., Rahmann, L., Öztireli, C., & Gross, M.H. (2018). [A Network Architecture for Point Cloud Classification via Automatic Depth Images Generation.](https://pdfs.semanticscholar.org/8348/589d78e6b7b9f8da5d5db9b5720dee0e79d5.pdf) 2018 IEEE/CVF Conference on Computer Vision and Pattern Recognition, 4176-4184.*

	用PointNet将3D点云转化成2D深度图,后用ResNet50分类

	详解：https://blog.csdn.net/cy13762633948/article/details/82780042
- 点云估计场景流
	>*[FlowNet3D++: Geometric Losses For Deep Scene Flow Estimation](https://arxiv.org/abs/1912.01438)Zirui Wang, Shuda Li,arxiv(2019)*

	提出向量的cosine distance loss来度量向量的夹角
##### Upsample
- PUNet
	>*Yu, Lequan & Li, Xianzhi & Fu, Chi-Wing & Cohen-Or, Daniel & Heng, Pheng-Ann. (2018). [PU-Net: Point Cloud Upsampling Network.](https://arxiv.org/abs/1801.06761) CVPR*
- PUGAN
  >*Li, R., Li, X., Fu, C., Cohen-Or, D., & Heng, P.A. (2019). [PU-GAN: a Point Cloud Upsampling Adversarial Network](https://liruihui.github.io/publication/PU-GAN/). ArXiv, abs/1907.10844.*
- DUP-Net
	>*Zhou, H., Chen, K., Zhang, W., Fang, H., Zhou, W., & Yu, N. (2018). [DUP-Net: Denoiser and Upsampler Network for 3D Adversarial Point Clouds Defense.](https://arxiv.org/abs/1812.11017) ICCV 2019.*
##### Latent representation
- Structure Net
	>*Mo, Kaichun & Guerrero, Leonidas. (2019). [StructureNet: hierarchical graph networks for 3D shape generation.](https://cs.stanford.edu/~kaichun/structurenet/) ACM Transactions on Graphics.*
- Point Cloud VAE
	>*Han, Z., Wang, X., Liu, Y., & Zwicker, M. (2019). [Multi-Angle Point Cloud-VAE: Unsupervised Feature Learning for 3D Point Clouds from Multiple Angles by Joint Self-Reconstruction and Half-to-Half Prediction.](https://arxiv.org/abs/1907.12704) ICCV 2019, abs/1907.12704.*
- Point cloud compression
	>*Huang, T., & Liu, Y. (2019). [3D Point Cloud Geometry Compression on Deep Learning.](https://dl.acm.org/doi/10.1145/3343031.3351061) MM '19.*
#### 4. 目标检测
- Cascade R-CNN
	>*Cai, Z., & Vasconcelos, N. (2017). [Cascade R-CNN: Delving Into High Quality Object Detection.](https://arxiv.org/abs/1712.00726) 2018 IEEE/CVF Conference on Computer Vision and Pattern Recognition, 6154-6162.*
	
	在提出候选框anchor box时，由于IoU（和GT的重叠部分）阈值设为0.5偏低，造成很多close false positive（稍微高于0.5，但是是负样本）重复无用的候选框，因此采用级联的方式逐步提升，在不同的阶段设置不同的阈值
	
	论文笔记：https://wanglichun.tech/algorithm/CascadeRCNN.html

	https://arleyzhang.github.io/articles/1c9cf9be/
	 
	https://github.com/ming71/CV_PaperDaily/blob/master/CVPR/Cascade-R-CNN-Delving-into-High-Quality-Object-Detection.md
	 
- CBNet：多模型特征融合
	>*Liu, Yudong & Wang, Yongtao & Wang, Siwei & Liang, TingTing & Zhao, Qijie & Tang, Zhi & Ling, Haibin. (2019). [CBNet: A Novel Composite Backbone Network Architecture for Object Detection.](https://arxiv.org/abs/1909.03625v1)*

#### 5. CNN结构分析
- 利用CNN中间层作为loss
	>*Zhang, R., Isola, P., Efros, A.A., Shechtman, E., & Wang, O. (2018). [The Unreasonable Effectiveness of Deep Features as a Perceptual Metric](https://arxiv.org/abs/1801.03924?utm_source=feedburner&utm_medium=feed&utm_campaign=Feed%3A+arxiv%2Fcs%2FCV+%28ArXiv.cs.CV%29). 2018 IEEE/CVF Conference on Computer Vision and Pattern Recognition, 586-595.*
  
- focus more on texture
	>*Geirhos, Robert & Rubisch, Patricia & Michaelis, Claudio & Bethge, Matthias & Wichmann, Felix & Brendel, Wieland. (2018). [ImageNet-trained CNNs are biased towards texture; increasing shape bias improves accuracy and robustness.](https://arxiv.org/abs/1811.12231) ICLR*
	
#### 6. 提取流场streamline特征
- 流场可视化：Flownet：streamlines的聚类和选择（去噪）
  更好的呈现可视化的效果，防止重叠现象等
  >*Han, J., Tao, J., & Wang, C. (2018). FlowNet: [A Deep Learning Framework for Clustering and Selection of Streamlines and Stream Surfaces.](https://pdfs.semanticscholar.org/d196/cb800c32afaebf2933f86f4fe1ab8b11e87b.pdf?_ga=2.34339045.533564957.1574860954-1135356387.1566352454) IEEE transactions on visualization and computer graphics.*
  
  将streamlines转化成体数据->输入3DCNN->encoder->latent value vector(特征向量)->decoder->生成体数据->与原数据计算loss
  
  得到**中间的特征向量表示**后，用t-SNE降维，用DBSCAN聚类
  
  如何生成中间的特征表示？？？（[3Dshape gan生成](#3dgan)）
  
- 流场压缩和重建
	>*Han, Jun & Tao, Jun & Zheng, Hao & Guo, Hanqi & Chen, Danny & Wang, Chaoli. (2019). [Flow Field Reduction Via Reconstructing Vector Data From 3-D Streamlines Using Deep Learning.](https://www3.nd.edu/~cwang11/research/cga19-ffr.pdf) IEEE Computer Graphics and Applications.*
	
	选取生成的streamlines经过压缩，通过CNN来重建流场（可用于Insitu）
	
	两步重建：
	1. streamline到LR向量场
	2. LR向量场经过deconv+residual到HR向量场
	
	Loss：MSE & Evaluation：PSNR
#### 7. 识别流场vortex
- 学习unsteady场的最优参考系参数

	> 1. *Kim, B., & Günther, T. (2019). [Robust Reference Frame Extraction from Unsteady 2D Vector Fields with Convolutional Neural Networks.](https://arxiv.org/abs/1903.10255) Comput. Graph. Forum,Eurovis 38, 285-295.*

	* input：2D向量场
	* output：参考系的旋转和平移参数θ
	
	由[TSR-TVD](#tsr)引用
	
	通过对steady的向量场进行变换获得train data（用来参考如何生成变形的vortex lic），输入unsteady的向量场得到其形变的参数，就可以准确识别其vortex。

- R-CNN识别分类vortex，先检测再分类
	> 2. *Strofer, C.M., Wu, J., Xiao, H., & Paterson, E.G. (2018). [Data-Driven, Physics-Based Feature Extraction from Fluid Flow Fields using Convolutional Neural Networks.](https://arxiv.org/abs/1802.00775) Commun. Comput. Phys.*
	* input:流场被分为三维网格，每个point都带有一个特征向量（类似RGB）：具有伽利略不变性的物理特征
	* Step 1：Region Proposal：目标检测，得到候选框
	* Step 2：分类,识别不同特性的流场
- R-CNN：基于流线图（后续工作[GenerativeMap：动态演化](#generativemap)）
	> 3. *X. Bai, C. Wang and C. Li, [A Streampath-Based RCNN Approach to Ocean Eddy Detection,](https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=8779643) in IEEE Access, vol. 7, pp. 106336-106345, 2019.*
- CNN识别分类vortex（逆时针、顺时针）直接检测
	> 4. *Bin, T.J. (2018). [CNN-based Flow Field Feature Visualization Method.](https://pdfs.semanticscholar.org/de16/9148f9c8484d175f92463af461a2bdfb3605.pdf) IJPE*
	- Step1 : 对流场进行分割，找到critical point，即v=0的点所处的网格。
	* input：用9×9×2的向量场，u,v双通道
	* output：对vortex分类，分成顺时针、逆时针和普通场
- CNN识别分类vortex，对每个点分类
	> 5. *Deng, Liang & Wang, Yueqing & Liu, Yang & Wang, Fang & Li, Sikun & Liu, Jie. (2018). [A CNN-based vortex identification method.](https://sci-hub.tw/10.1007/s12650-018-0523-1#) Journal of Visualization. 22. 10.1007/s12650-018-0523-1.*
	- Step 1：用IVD方法给每个点打上标签，vortex：1，non-vertex：0
	- Step 2：对于每个点，取一个15×15的patch，放进cnn训练，二分类确定这个点是否属于vortex
### 三. GAN

最新发展和应用 https://www.zhihu.com/question/52602529/answers/updated
- <span id="gan">综述 : 损失函数、对抗架构、正则化、归一化和度量方法</span>
	>*Kurach, K., Lucic, M., Zhai, X., Michalski, M., & Gelly, S. (2018). [A Large-Scale Study on Regularization and Normalization in GANs.](https://arxiv.org/abs/1807.04720) ICML.*
	
	资料：https://daiwk.github.io/posts/cv-gan-overview.html
- VAEGAN
	>*Larsen, A.B., Sønderby, S.K., Larochelle, H., & Winther, O. (2015). [Autoencoding beyond pixels using a learned similarity metric.](https://arxiv.org/pdf/1512.09300.pdf) ICML.*
- 条件转移GAN：更少的数据要求+更多分类
	>*Wu, C., Wen, W., Chen, Y., & Li, H. (2019). [Conditional Transferring Features: Scaling GANs to Thousands of Classes with 30% Less High-quality Data for Training.] (https://www.arxiv-vanity.com/papers/1909.11308/) CVPR*
- BiGan提升
	>*Pablo Sánchez-Martín, Pablo M. Olmos. (2019) [Improved BiGAN training with marginal likelihood equalization](https://arxiv.org/abs/1911.01425) Arxiv.*
	
#### 1. VOLUME
- INSITUNet:流场可视化图片生成(探索参数空间)
  >*He, W., Wang, J., Guo, H., Wang, K., Shen, H., Raj, M., Nashed, Y.S., & Peterka, T. (2019). [InSituNet: Deep Image Synthesis for Parameter Space Exploration of Ensemble Simulations.](https://arxiv.org/abs/1908.00407) IEEE transactions on visualization and computer graphics.*
simulation, vi-sual mapping, and view parameters

  输入模拟参数(simulation, visual mapping, and view parameters)，实时生成可视化图像（RGB）->可以探索参数空间对可视化图像的影响
  * in&out对于不同的**视觉参数**生成**可视化图像**，作为输入输出输入模型
  * 模型结构：
	
	* GAN:卷积回归模型（生成图像）+判别器D（对比GroundTruth）:内部都是resnet
		
	GAN结构来自于已有GAN模型（[SN-GAN](#sngan)和[ganlandsacpe](#gan)）
		
	* 特征提取网络：VGGnet
		
	思路来自图像合成[Perceptual Losses](#perceptual)（CNN）和[SRGAN](#srgan)和[Deep feature VAE](#deepae)
	
	
- 体数据渲染生成
  >*Berger, M., Li, J., & Levine, J.A. (2017). [A Generative Model for Volume Rendering.](https://arxiv.org/abs/1710.09545) IEEE Transactions on Visualization and Computer Graphics, 25, 1636-1650.*

#### 2. GAN图像生成/迁移
- <span id="sngan">SN-GAN：谱归一化，解决训练不稳定</span>
	>*Miyato, Takeru & Kataoka, Toshiki & Koyama, Masanori & Yoshida, Yuichi. (2018).(oral) [Spectral Normalization for Generative Adversarial Networks.](https://arxiv.org/abs/1802.05957) IEEE ICLR*
- 图像迁移：StarGAN v2(reference:552)
	>*Yunjey Choi, Youngjung Uh, Jaejun Yoo, Jung-Woo Ha.(2019) [StarGAN v2: Diverse Image Synthesis for Multiple Domains](https://www.arxiv-vanity.com/papers/1912.01865/)*
	
	Github : https://github.com/clovaai/stargan-v2
	
	从一个领域转换到另外一个领域的图像合成。现有的GAN模型为了实现在k个不同的风格域上进行迁移，需要构建k∗(k−1)个生成器，即只解决了一个领域到另一个领域的转换，对于每一个领域转换，都需要重新训练一个模型去解决。并且还不能跨数据集训练（标注不能复用）。StarGAN正是为了解决跨多个域、多个数据集的训练而提出的。
	
	将域信息和图片一起输入进行训练，并在域标签中加入mask vector，便于不同的训练集进行联合训练
	

#### 3. 3Dgan 

- 生成3D物体的体数据

  >*Wu, J., Zhang, C., Xue, T., Freeman, W.T., & Tenenbaum, J.B. (2016). [Learning a Probabilistic Latent Space of Object Shapes via 3D Generative-Adversarial Modeling](http://3dgan.csail.mit.edu/) NIPS.*
  
  >*Girdhar, Rohit & Fouhey, David & Rodriguez, Mikel & Mulam, Harikrishna. (2016). [Learning a Predictable and Generative Vector Representation for Objects.](https://arxiv.org/abs/1603.08637) ECCV*
  
  >*Mo, K., Guerrero, P., Yi, L., Su, H., Wonka, P., Mitra, N., & Guibas, L.J. (2019). [StructureNet: Hierarchical Graph Networks for 3D Shape Generation.](https://cs.stanford.edu/~kaichun/structurenet/) ArXiv, abs/1908.00575.*
  

### 四. 超分辨率

#### 1. Volume and Isosurface
- 体数据的上采样(CNN)
  >*Zhou, Z., Hou, Y., Wang, Q., Chen, G., Lu, J., Tao, Y., & Lin, H. (2017). Volume upscaling with convolutional neural networks. CGI.*
- Isosurface（CNN）
	>*Weiss, S., Chu, M., Thürey, N., & Westermann, R. (2019). [Volumetric Isosurface Rendering with Deep Learning-Based Super-Resolution.](https://arxiv.org/abs/1906.06520) ArXiv, abs/1906.06520.*
	
	training data : LR输入是通过raytracer直接得到的render数据（低精度+高精度），而不是通过SR下采样的，因此输入非常noise并给任务提出了很大的困难。
	
	跑了500个序列（每个序列10帧）的不同相机角度的render，低精度的128\*128,再将其随机截取到32\*32作为training data，label是计算得到的Ambient occlusion
	
	loss function : mse loss + perceptual loss(pretrained VGG的隐藏层feature) + temporal coherence loss
	
	采用[FRVSR-Net](#frvsr)的网络结构，全卷积网络
	
	User case：remote vis（只传LR render，用模型进行上采样）/In situ vis(只能存某几个时间步的data，对中间缺少的帧进行插值)/focus+context volume visualization
	
	Github : https://github.com/shamanDevel/IsosurfaceSuperresolution


#### 2. Image
- SRCNN（CNN）
  >*Dong, Chao & Loy, Chen Change & He, Kaiming & Tang, Xiaoou. (2014). [Image Super-Resolution Using Deep Convolutional Networks.](https://arxiv.org/pdf/1501.00092.pdf) IEEE Transactions on Pattern Analysis and Machine Intelligence. 38. 10.1109/TPAMI.2015.2439281.*
- <span id="enhancenet">EnhanceNet(CNN)</span>
	>*Sajjadi, Mehdi S. M. & Schölkopf, Bernhard & Hirsch, Michael. (2016). [EnhanceNet: Single Image Super-Resolution through Automated Texture Synthesis.](https://arxiv.org/abs/1612.07919)*

	提出不同于传统MSE的损失函数：perceptual loss+texture loss+Adversarial loss
	
	GitHub : https://github.com/msmsajjadi/EnhanceNet-Code
- <span id="srgan">SRGAN</span>
  >*Ledig, C., Theis, L., Huszar, F., Caballero, J., Cunningham, A., Acosta, A., Aitken, A., Tejani, A., Totz, J., Wang, Z., & Shi, W. (2016). [Photo-Realistic Single Image Super-Resolution Using a Generative Adversarial Network](https://arxiv.org/abs/1609.04802)2017 IEEE (CVPR), 105-114.*
- ESRGAN (**SOTA2**)
  >*Wang, X., Yu, K., Wu, S., Gu, J., Liu, Y., Dong, C., Qiao, Y., & Loy, C.C. (2018). [Enhanced Super-Resolution Generative Adversarial Networks](https://arxiv.org/abs/1809.00219). ECCV Workshops.*
- GAN生成高分辨率的突破工作
  >*Wang, T., Liu, M., Zhu, J., Tao, A., Kautz, J., & Catanzaro, B. (2017). [High-Resolution Image Synthesis and Semantic Manipulation with Conditional GANs.](https://arxiv.org/abs/1711.11585) 2018 IEEE/CVF Conference on Computer Vision and Pattern Recognition, 8798-8807.*
  
  知乎笔记：https://zhuanlan.zhihu.com/p/35955531
- DUpsampling : 超越双线性插值
	>*Tian, Zhi & Shen, Chunhua & Tong, He & Yan, Youliang. (2019). [Decoders Matter for Semantic Segmentation: Data-Dependent Decoding Enables Flexible Feature Aggregation.](https://arxiv.org/abs/1903.02120)*
	
	Github ： https://github.com/LinZhuoChen/DUpsampling non-official
	
	笔记：https://www.zybuluo.com/Team/note/1442573
- SAN (**SOTA**)
	>*Dai, T., Cai, J., Zhang, Y., Xia, S., & Zhang, L. (2019). [Second-Order Attention Network for Single Image Super-Resolution.](http://openaccess.thecvf.com/content_CVPR_2019/papers/Dai_Second-Order_Attention_Network_for_Single_Image_Super-Resolution_CVPR_2019_paper.pdf) CVPR.*
	
	Non-locally Enhanced Residual Group (NLRG) including:
	
	Region-level non-local module (RL-NL) and Local-source residual attention group (LSRAG)
	
	Also have second-order channel attention
##### 2.1 无监督或zero shot学习 （GAN）
对单幅自然图像中的图像内部分布进行建模，训练样本是单幅图像不同尺度下的采样图像。

- ZSSR : ZeroShot Super Resolution
	>*A. Shocher, N. Cohen, and M. Irani, [zero-shot super-resolution using deep internal learning,](https://arxiv.org/abs/1712.06087) inCVPR, 2018.*
	
	GitHub : https://github.com/assafshocher/ZSSR
- (oral) InGAN
	>*Shocher, A., Bagon, S., Isola, P., & Irani, M. (2018). [InGAN: Capturing and Remapping the "DNA" of a Natural Image.](http://www.wisdom.weizmann.ac.il/~vision/ingan/) in ICCV 2019*

	GitHub : https://github.com/assafshocher/InGAN
- (BestPaper) SinGAN
	>*Shaham, Tamar & Dekel, Tali & Michaeli, Tomer. (2019). [SinGAN: Learning a Generative Model from a Single Natural Image.](http://webee.technion.ac.il/people/tomermic/SinGAN/SinGAN.htm) in ICCV 2019*
	
	GitHub : https://github.com/tamarott/SinGAN
- 无监督分辨率提升（CNN）
  >*Lugmayr, Andreas et al. (2019).[Unsupervised Learning for Real-World Super-Resolution](https://128.84.21.199/abs/1909.09629) ICCV*	
- 
  >*Zhao, T., Ren, W., Zhang, C., Ren, D., & Hu, Q. (2018). [Unsupervised Degradation Learning for Single Image Super-Resolution](https://arxiv.org/abs/1812.04240v1). CVPR, abs/1812.04240.*
  学习了cyclegan（风格迁移）中image to image的思想

  [知乎笔记](https://zhuanlan.zhihu.com/p/52237543)

#### 3. Video

Video SR一般包括四个步骤：
1. feature extraction 
2. Alignment:optical-flow-based model:warp / deformable conv(eg.EDVR) / dynamic filter
3. Fusion:recurrent network(eg.[FRVSR](#frvsr)) / Attention(eg.EDVR)
4. Reconstruction
- EDVR (**当前SOTA**)
	>*Wang, X., Chan, K.C., Yu, K., Dong, C., & Loy, C.C. (2019). [EDVR: Video Restoration with Enhanced Deformable Convolutional Networks.](https://arxiv.org/abs/1905.02716v1) CVPR Workshops.*
	
	- Alignment: Pyramid, Cascading and Deformable convolutions
	- Fusion: Temporal and Spatial Attention
- DUF (SOTA2)
	>*Jo, Younghyun & Oh, Seoung & Kang, Jaeyeon & Kim, Seon. (2018). [Deep Video Super-Resolution Network Using Dynamic Upsampling Filters Without Explicit Motion Compensation.](http://openaccess.thecvf.com/content_cvpr_2018/html/Jo_Deep_Video_Super_Resolution_CVPR_2018_paper.html) CVPR.*
- RBPN (SOTA3)
	>*Haris, M., Shakhnarovich, G., & Ukita, N. (2019). [Recurrent Back-Projection Network for Video Super-Resolution.](https://arxiv.org/abs/1903.10128v1) CVPR.*
- <span id="FRVSR">多帧图像超分 : FRVSR-Net (SOTA4)</span>
	>*M. S. Sajjadi, R. Vemulapalli, and M. Brown. [Frame-recurrent video super-resolution.](https://arxiv.org/abs/1801.04590) In Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition, pp. 6626–6634, 2018.*
	
	SRNet采用[EnhanceNet](#enhancenet)
- TecoGAN
	>*Chu, M., Xie, Y., Leal-Taixé, L., & Thürey, N. (2018). [Temporally Coherent GANs for Video Super-Resolution (TecoGAN).](https://arxiv.org/abs/1811.09393) ArXiv, abs/1811.09393.*
	
- 多帧图像的超分（HighRes-net）
	>*Anonymous. [Highres-net: Multi-frame super-resolution by recursive fusion.](https://openreview.net/forum?id=HJxJ2h4tPr&noteId=HJxJ2h4tPr) In Submitted to International Conference on Learning Representations, 2020. under review ICLR.*
	将多帧图像做recursive fusion提取背景参考系，适用于卫星云图等背景运动较少的情况
	
	Github : https://github.com/ElementAI/HighRes-net 多帧超分
- 太阳磁图的超分辨率（使用HighRes-net）
	>*Gitiaux, Xavier, Shane Maloney. (2019) . [Probabilistic Super-Resolution of Solar Magnetograms: Generating Many Explanations and Measuring Uncertainties.](https://arxiv.org/abs/1911.01486) In Fourth Workshop on Bayesian Deep Learning (NeurIPS 2019), Vancouver, Canada.*	
	
#### 4. Video interpolation (Temporal super-resolution)
- DAIN(**SOTA**)
	>*Bao, W., Lai, W., Ma, C., Zhang, X., Gao, Z., & Yang, M. (2019). [Depth-Aware Video Frame Interpolation.](https://arxiv.org/abs/1904.00830) 2019 IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR), 3698-3707.*
	包含4个module:
	1. optical flow estimation
	2. depth estimation
	3. context extraction
	4. interp kernal estimation
	
- Super Slomo

	>*Jiang, H., Sun, D., Jampani, V., Yang, M., Learned-Miller, E.G., & Kautz, J. (2017). [Super SloMo: High Quality Estimation of Multiple Intermediate Frames for Video Interpolation.](https://arxiv.org/abs/1712.00080) 2018 IEEE/CVF Conference on Computer Vision and Pattern Recognition, 9000-9008.*
	
- CyclicGen

	>*Liu, Y., Liao, Y., Lin, Y., & Chuang, Y. (2019). [Deep Video Frame Interpolation Using Cyclic Frame Generation.](https://www.csie.ntu.edu.tw/~cyy/publications/papers/Liu2019DVF.pdf) AAAI.*
	
	propose cycle consistent loss to make best use of training data.
#### 5. 时序流场

- **SR**时序流体数据tempoGAN（using **Conditional GAN** & 最近邻插值）
	>*Xie, You & Franz, Erik & Chu, Mengyu & Thuerey, Nils. (2018). [tempoGAN: A Temporally Coherent, Volumetric GAN for Super-resolution Fluid Flow.](https://arxiv.org/abs/1801.09710) ACM Transactions on Graphics. 37. 10.1145/3197517.3201304.*
	
	免去了物理模拟时需要一步步进行temporal advect的情况，只需要输入一帧低精度的x，就可以生成一系列高精度的yt-1~yt+1

	涡流运动的高精度可能会有多个版本，只要满足参考数据的时空分布就是一个可行解。
	
	training data：x是HR的y经过下采样的数据
	
	test data：是同一个模拟但训练集中没有的不同参数的数据LR+HRpairs

	- input：单时间步第t步的低分辨率流体的密度数据(16^3)+速度场+旋度场(作为条件输入去推断密度)
	- output: 高分辨率的第t步的密度数据(64^3)
	- 网络架构：
		- 最近邻插值作为上采样方法（而不是反卷积，基于[Deconvolution and Checkerboard Artifacts](https://distill.pub/2016/deconv-checkerboard/)：因为反卷积和sub-pixel会产生重叠）
		- generator采用全卷积层（可以任意size输入）residual block（尝试过U-Net）
		- discriminator采用卷积+Leakyrelu+全连接（输出score）
	- Loss Function：
		- adversarial loss + feature loss(自身的discriminator的隐藏层) + temporal loss
		- temporal loss: 上一帧G生成的结果经过warp与当前帧做对比（有一个discriminator判断是来自gt还是来自生成）
- Multi-Pass GAN
	>*Werhahn, M., Xie, Y., Chu, M., & Thürey, N. (2019). [A Multi-Pass GAN for Fluid Flow Super-Resolution.](https://arxiv.org/abs/1906.01689) PACMCGIT, 2, 10:1-10:21.*
	- 更高的SR倍数
	- 将XY平面slice和Z轴分别训练，减少时间
	- 有temporal coherence
- TSR-TVD : 时序的超分辨率(RNN+GAN)
	>*Han, J., & Wang, C. (2019). [TSR-TVD: Temporal Super-Resolution for Time-Varying Data Analysis and Visualization ](https://www3.nd.edu/~cwang11/research/vis19-tsr.pdf). IEEE TVCG.*
  
- <span id="generativemap">GenerativeMap : 流场局部特征的动态演化过程（BiGan：Density map）</span>
	>*Chen, C., Wang, C., Bai, X., Zhang, P., & Li, C. (2019). [GenerativeMap: Visualization and Exploration of Dynamic Density Maps via Generative Learning Model.](https://pdfs.semanticscholar.org/60c6/192a1957ef176690e3d0299b71892f7b3768.pdf?_ga=2.160779069.1624041468.1573624621-1882078897.1570782246) IEEE transactions on visualization and computer graphics.*
	* Step1 ：将局部密度图encode成latent vector
	* Step2 ：对隐向量进行线性插值生成中间动态演变的过程
	* Step3 ：将插值的隐向量decode成图像
	- BiGAN结构：
	
	分别训练decoder：假分布z--->假数据x和encoder：真数据x---->真分布z 
	
	再训练discriminator区分这两个对


### 五. Deblur
#### 1. Image Deblur
- DeblurGAN
	>*Kupyn, O., Martyniuk, T., Wu, J., & Wang, Z. (2019). [DeblurGAN-v2: Deblurring (Orders-of-Magnitude) Faster and Better.](https://arxiv.org/abs/1908.03826) ArXiv, abs/1908.03826.*
- IKC
	>*Gu, J., Lu, H., Zuo, W., & Dong, C. (2019). [Blind Super-Resolution With Iterative Kernel Correction.](https://arxiv.org/abs/1904.03377) CVPR.*
	
	>*Bai, Y., Jia, H., Jiang, M., Liu, X., Xie, X., & Gao, W. (2019). [Single Image Blind Deblurring Using Multi-Scale Latent Structure Prior.](https://arxiv.org/abs/1906.04442) ArXiv, abs/1906.04442.*

	>*Li, Lerenhan & Pan, Jinshan.(2018). [Learning a Discriminative Prior for Blind Image Deblurring.](https://arxiv.org/pdf/1803.03363.pdf)CVPR.*
	
- SRN: classical

	>*Tao, Xin & Gao, Hongyun & Jia, Jiaya. (2018). [Scale-Recurrent Network for Deep Image Deblurring.](https://arxiv.org/abs/1802.01770) CVPR.*
#### 2. Video Deblur
### 六. 视频预测
#### 1. GAN
- <span id="videogradient">视频预测Lecun(GAN)</span>
	>*MATHIEU M., COUPRIE C., LECUN Y.: [Deep multi-scale video prediction beyond mean square error.](https://arxiv.org/abs/1511.05440) arXiv:1511.05440. 4*
	
	采用梯度损失
	>*Lotter, W., Kreiman, G., & Cox, D.D. (2015). [Unsupervised Learning of Visual Structure using Predictive Generative Networks](https://arxiv.org/abs/1511.06380). ArXiv, abs/1511.06380.*

	>*Ruben Villegas, Arkanath Pathak. (2019). [High Fidelity Video Prediction with Large Stochastic Recurrent Neural Networks](https://arxiv.org/abs/1911.01655v1) NeurIPS*
	
- Video generaion
	>*Chu, M., Xie, Y., Mayer, J., Leal-Taixé, L., & Thürey, N. (2018). [Learning Temporal Coherence via Self-Supervision for GAN-based Video Generation.](https://arxiv.org/abs/1811.09393) CVPR*
	
#### 2. CNN
- 二阶视频插值
	>*Xu, X., Si-Yao, L., Sun, W., Yin, Q., & Yang, M. (2019). [Quadratic video interpolation.](https://arxiv.org/abs/1911.00627) NeurlIPS 2019, abs/1911.00627.*

	包含加速度信息的二阶光流运动的预测，通过“光流逆转层”来预测光流的逆运动，以进行插值。并通过自适应滤波器减小逆转后的震荡。
	
- Deep Voxel Flow 
	>*(oral)Liu, Z., Yeh, R.A., Tang, X., Liu, Y., & Agarwala, A. (2017). Video Frame Synthesis Using Deep Voxel Flow. 2017 IEEE International Conference on Computer Vision (ICCV), 4473-4481.*
  
  利用现有的video进行无监督的学习
### 七. NLP
#### 1. Attention
- 可解释性
	>*Vashishth, S., Upadhyay, S., Tomar, G.S., & Faruqui, M. (2019). [Attention Interpretability Across NLP Tasks.](https://www.arxiv-vanity.com/papers/1909.11218/)*
#### 2. Transformer
- Transformer
	>*Vaswani, A., Shazeer, N., Parmar, N., Uszkoreit, J., Jones, L., Gomez, A.N., Kaiser, L., & Polosukhin, I. (2017). [Attention Is All You Need.](https://arxiv.org/abs/1706.03762) NIPS.*
#### 3. Memory
- Memory机制：attention中加入存储器层(Facebook)
	>*Lample, G., Sablayrolles, A., Ranzato, M., Denoyer, L., & J'egou, H. (2019). [Large Memory Layers with Product Keys](https://arxiv.org/abs/1907.05242). ArXiv, abs/1907.05242.*

### 八. HPC

- 对HPC中的异常进行可视化(转化成向量表示)
  >*Xie, C., Xu, W., & Mueller, K. (2018). A Visual Analytics Framework for the Detection of Anomalous Call Stack Trees in High Performance Computing Applications. IEEE Transactions on Visualization and Computer Graphics, 25, 215-224.*
#### 并行粒子追踪
- Domain traversal:空间组织方式(Hierarchical PageMap)
- 并行粒子追踪综述
	>*Zhang, Jiang & Yuan, Xiaoru. (2018). [A survey of parallel particle tracing algorithms in flow visualization.](http://vis.pku.edu.cn/research/publication/jov18-pptsurvey.pdf) Journal of Visualization. 21. 10.1007/s12650-017-0470-2.*
- CPU，I/O线程和计算线程混合
	>*Camp, D., Garth, C., Childs, H., Pugmire, D., & Joy, K.I. (2011). [Streamline Integration Using MPI-Hybrid Parallelism on a Large Multicore Architecture. ](https://crd.lbl.gov/assets/pubs_presos/LBNL-4563E.pdf)IEEE Transactions on Visualization and Computer Graphics, 17, 1702-1713.*

	* 一个task负责一个单独的计算任务：一部分data blocks or particles
	* 两种模式
	1. MPI-Only:一个task=一个core=多个（4个）线程

	2. MPI-Hybrid:一个node==一个task==多个（4个）core=多个（4个）线程（只是计算线程的个数）
		* 线程在两个队列上的同步通过：**互斥锁和条件原语**，task之间的同步通过**MPI**
	
	* Task parallelism：每个Task负责一部分particles，有一块数据缓存，两个粒子队列，n个I/O线程和n个计算线程。I/O线程负责加载inactive队列中粒子需要的数据，计算线程负责advect。

	* Data parallelism：每个Task负责一部分数据块，两个队列，1个通信线程，n-1个计算线程。通信线程负责将超出界限的粒子送到目的地Task，接受其他task发来的粒子，计算线程负责advect

- 上述结构+GPU加速+Data Parallelism
	>*D. Camp, H. Krishnan,[GPU acceleration of particle advection workloads in a parallel, distributed memory setting.](https://pdfs.semanticscholar.org/89cd/edbc0f33fc53a3abacb07c835e18bb9659cd.pdf?_ga=2.15252150.1459940645.1571104676-1882078897.1570782246) In EGPGV13: Eurographics Symposium on Parallel Graphics and Visualization, pp. 1–8, 2013*

	* 一个node==一个task==多个（8个）core，一个task（单独计算，负责一部分 data block）下有多个（8个）线程（对应core的个数：只是work线程的个数）：一个主线程负责通信，7个work线程负责计算。

	* 线程同步通过：**互斥锁**，task之间通信通过：**MPI点对点非阻塞通信**

	* Data parallelism：每个task不断迭代下列操作，直到所有task都结束：1）一组粒子在GPU/CPU advect 2）将结束发射的粒子放进对应的队列中 3）通信：将粒子传递给目的地的task。 
	
- 自适应，分解时间段计算SFM,CPU+GPU，task+data parallelism
	>*Guo, H., He, W., Seo, S., Shen, H., Constantinescu, E.M., Liu, C., & Peterka, T. (2018). [Extreme-Scale Stochastic Particle Tracing for Uncertain Unsteady Flow Visualization and Analysis.](https://www.mcs.anl.gov/~tpeterka/papers/2018/guo-tvcg18-paper.pdf) IEEE Transactions on Visualization and Computer Graphics, 25, 2710-2724.*
	
	自适应的调整每个位置i进行的蒙特卡洛模拟的次数：每一次迭代，一个batch的粒子会从第i个网格的中心射出，计算这轮迭代之前所有的粒子的终点的概率分布，直到概率分布收敛，迭代结束（p的信息熵的插值来表示终止）
	* 并行架构
		* 一组进程单独计算负责一个任务（**一部分seeds**+**全部数据**)；
		* 组内数据分布式存储：每个进程负责一部分数据;如一组有4个进程，总共有32块数据，则每个进程负责8块数据
		* 一个node = 一个task进程 （进程间通过MPI通信）= 16个core = 64个线程：一个主线程负责MPI通信，63个work线程负责计算
		* Data parallelism：每个进程里有两组任务队列，work和send：主线程将send队列的任务送往目标进程去执行，计算线程计算work队列里的任务，如果particle超出该进程的数据范围，就创建一个相关任务加入相应目的地的send队列。

### 九. Flow Visualization

- 云层及风场可视化案例
	>*Rimensberger, N., Gross, M.H., & Günther, T. (2019). [Visualization of Clouds and Atmospheric Air Flows.](https://pdfs.semanticscholar.org/90c2/369fc8aac8fed7a8b7818d32f1aee7571ee8.pdf?_ga=2.220039578.204005370.1571192150-1135356387.1566352454) IEEE Computer Graphics and Applications, 39, 12-25.*
	
	向量场的旋度、散度的volume rendering和iso-surface，pathlines的可视化，FTLE的计算：[unbiased 蒙特卡洛渲染](#mcftle)
- 生成objective vortex：通过调整成最优参考系
	>*Günther, T., Gross, M.H., & Theisel, H. (2017). [Generic objective vortices for flow visualization.](https://cgl.ethz.ch/publications/papers/paperGun17c.php) ACM Trans. Graph., 36, 141:1-141:11.*
	
- 将unsteady的流场分解成steady的参考系和环境流
	>*Rojo, I.B., & Gunther, T. (2019). [Vector Field Topology of Time-Dependent Flows in a Steady Reference Frame.](https://cgl.ethz.ch/publications/papers/paperIbr19c.php) IEEE transactions on visualization and computer graphics.*
	
	1) 对流场transform(包括标量和向量)
	
	2) 最小二乘求解最优参考系
	
	3) 计算得ambient flow/环境流
	
	4) 计算critical point，separatrices，vortex corelines
#### 1. FTLE
- <span id="mcftle">MCFTLE</span>
	>*T.G ̈ unther,A.Kuhn,andH.Theisel. [MCFTLE : Monte Carlo rendering of finite-time Lyapunov exponent fields.](https://people.inf.ethz.ch/~gutobia/publications/Guenther16EuroVisb.html) Computer Graphics Forum (Proc. EuroVis), 35(3):381–390, 2016.*
	>*Rojo, I.B., Groß, M., & Gonther, T. (2019). [Accelerated Monte Carlo Rendering of Finite-Time Lyapunov Exponents.](https://cgl.ethz.ch/publications/papers/paperIbr19b.php) IEEE transactions on visualization and computer graphics.*

#### 2. Fluid simulation
- Deep Fluids : 降噪自编码器-生成**向量场** 模拟流体 from parameters
	>*Kim, B., Azevedo, V.C., Thürey, N., Kim, T., Gross, M.H., & Solenthaler, B. (2018). [Deep Fluids: A Generative Network for Parameterized Fluid Simulations.](https://cgl.ethz.ch/publications/papers/paperKim19a.php) Comput. Graph. Forum, 38, 59-70.*
	参数生成向量场，training data是运行的一系列不同参数的模拟
	
	test data : 训练集中没有出现过的参数
	
	AE(*Stacked denoising autoencoders: Learning useful representations in a deep network with a local denoising criterion.* J. Mach. Learn. Res. 11(2010)): 类似于 conv autoencoder
	
	初始的向量场------(encoder)---->[参数向量化的向量场+每一步的输入参数向量(如烟源头的x坐标+宽度)]---------(加了residual模块的CNN:stage之间深度不变=128,空间上采样 * 2)(等价于decoder)----->模拟生成每一个时间步的向量场

	1)训练一个encoder将向量场表示出n维参数向量**c**:(**z**(参数化的向量场)+**p**(输入参数))
	
	2)从t=0开始,递归计算:将上一步的参数向量**c**和**p**的残差**Δp**拼接,作为输入**x**,输入全连接层
	
	3)全连接层学习输出**z**的残差**Δz**,和上一步的参数化的向量场**z**t相加,得到**z**t+1
	
	4)将**z**t+1和**p**t+1(已知)拼接得到**c**t+1,作为generator的输入
	
	5)generator生成t+1时刻的向量场
	
	- LOSS Function: 
		- autoencoder的loss:encoding生成的参数p的L2损失(线性回归的标准loss)
		- generator的loss:生成的向量场L1损失(对于不可压缩流场采用向量场的旋度) + 向量场的梯度的L1损失(类似的梯度loss同于[lecun的video prediction论文中(2015)](#videogradient)中)
		- 时间转移的损失:窗口大小为w=30内的每一个时间步的生成的**Δz**的L2损失的平均值
- Latent space of fluid flow
	>*Wiewel, S., Becher, M., & Thürey, N. (2018). [Latent-space Physics: Towards Learning the Temporal Evolution of Fluid Flow.](https://arxiv.org/abs/1802.10123) Comput. Graph. Forum, 38, 71-82.*
	
	AE(*Stacked convolutional auto-encoders for hierarchical feature extraction.* Proc. ICANN (2011), 52–59. 3)
	
	+ LSTM(类似机翻的seq2seq模型，encoder+decoder) : 给定前n步的数据，预测未来o个时间步的流体数据（速度v, 压力p）
      
	AE的encoder将n步的数据x映射到m维空间(latent space), 输入作为encoder的lstm，得到c维向量，输入到decoder的lstm，得到o个m维的时间步，最后通过AE的decoder得到预测的流体数据
	
- Scala Flow:从视频流生成流体数据
	>*[ScalarFlow: A Large-Scale Volumetric Data Set of Real-world Scalar Transport Flows for Computer Animation and Machine Learning](https://ge.in.tum.de/publications/2019-tog-eckert/)*

#### 3. Streamline selection
- Survey
	>*Sane, Sudhanshu. [Strategies for Seed Placement and Streamline Selection.](https://www.cs.uoregon.edu/Reports/AREA-201904-Sane.pdf) (2019).*
	
	>*Sane, S., Bujack, R., Garth, C. and Childs, H. (2020), [A Survey of Seed Placement and Streamline Selection Techniques.](https://diglib.eg.org/bitstream/handle/10.1111/cgf14036/v39i3pp785-809.pdf) Computer Graphics Forum, 39: 785-809.*
- Manual system for seeds selection
	>*Laramee, R. (2002).  [Interactive 3D Flow Visualization Using a Streamrunner.](https://cronfa.swan.ac.uk/Record/cronfa24627/Download/0024627-11122015103342.pdf) CHI 2002 Conference on HumanFactors in Computing Systems, Extended Abstracts*
	
	>*Laramee, Robert & Weiskopf, D. & Schneider, Juergen & Hauser, Helwig. (2004). [Investigating swirl and tumble flow with a comparison of visualization techniques.](https://www.researchgate.net/profile/Juergen_Schneider5/publication/4112404_Investigating_swirl_and_tumble_flow_with_a_comparison_of_visualization_techniques/links/543401d20cf294006f73440e/Investigating-swirl-and-tumble-flow-with-a-comparison-of-visualization-techniques.pdf) Proc. IEEE Visualization. 2004. 51- 58. 10.1109/VISUAL.2004.59.*
	
	>*Fenglin Tian, Lingqi Cheng & Ge Chen (2020) [Transfer function-based 2D/3D interactive spatiotemporal visualizations of mesoscale eddies, International Journal of Digital Earth,](https://www.tandfonline.com/doi/citedby/10.1080/17538947.2018.1543364?scroll=top&needAccess=true)*
	streamline的可视化和交互方式。
- Reconstruct vector field from streamline
	
	>*L. Xu, T. Lee and H. Shen, ["An Information-Theoretic Framework for Flow Visualization,"](https://www.researchgate.net/profile/Teng_Yok_Lee2/publication/224186391_An_Information-Theoretic_Framework_for_Flow_Visualization/links/5571712c08aedcd33b293a9b.pdf) in IEEE Transactions on Visualization and Computer Graphics, vol. 16, no. 6, pp. 1216-1224, Nov.-Dec. 2010, doi: 10.1109/TVCG.2010.131.*
	
	采用GVF，对流线上的速度进行初始化。
	
	>*J. Tao, J. Ma, C. Wang and C. Shene, ["A Unified Approach to Streamline Selection and Viewpoint Selection for 3D Flow Visualization,"](https://www.researchgate.net/profile/Jun_Ma22/publication/228065955_A_Unified_Approach_to_Streamline_Selection_and_Viewpoint_Selection_for_3D_Flow_Visualization/links/558b428408ae02c9d1f95a22/A-Unified-Approach-to-Streamline-Selection-and-Viewpoint-Selection-for-3D-Flow-Visualization.pdf) in IEEE Transactions on Visualization and Computer Graphics, vol. 19, no. 3, pp. 393-406, March 2013, doi: 10.1109/TVCG.2012.143.*
	
	考虑半径位0.5个网格距离内的streamline的高斯平均的向量值。及streamline上点的权重为高斯权重。GVF的mu=0.1

	>*C. Xu and J. L. Prince. [Gradient vector flow: A new external force for snakes.](http://iacl.ece.jhu.edu/pubs/p087c-ieee.pdf) InCVPR, pages 66–71, 1997.*
	
	初始化一个场，并逼近场内的物体得到最终场。

	
