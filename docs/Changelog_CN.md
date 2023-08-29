### 20230813更新
1-常规bug修复
 - 保存频率总轮数最低改为1 总轮数最低改为2
 - 修复无pretrain模型训练报错
 - 增加伴奏人声分离完毕清理显存
 - faiss保存路径绝对路径改为相对路径
 - 支持路径包含空格（训练集路径+实验名称均支持，不再会报错）
 - filelist取消强制utf8编码
 - 解决实时变声中开启索引导致的CPU极大占用问题

2-重点更新
 - 训练出当前最强开源人声音高提取模型RMVPE，并用于RVC的训练、离线/实时推理，支持pytorch/onnx/DirectML
 - 通过pytorch-dml支持A卡和I卡的
（1）实时变声（2）推理（3）人声伴奏分离（4）训练暂未支持，会切换至CPU训练；通过onnx_dml支持rmvpe_gpu的推理

### 20230618更新
- v2增加32k和48k两个新预训练模型
- 修复非f0模型推理报错
- 对于超过一小时的训练集的索引建立环节，自动kmeans缩小特征处理以加速索引训练、加入和查询
- 附送一个人声转吉他玩具仓库
- 数据处理剔除异常值切片
- onnx导出选项卡

失败的实验：
- ~~特征检索增加时序维度：寄，没啥效果~~
- ~~特征检索增加PCAR降维可选项：寄，数据大用kmeans缩小数据量，数据小降维操作耗时比省下的匹配耗时还多~~
- ~~支持onnx推理（附带仅推理的小压缩包）：寄，生成nsf还是需要pytorch~~
- ~~训练时在音高、gender、eq、噪声等方面对输入进行随机增强：寄，没啥效果~~
- ~~接入小型声码器调研:寄，效果变差~~

todolist：
- ~~训练集音高识别支持crepe：已经被RMVPE取代，不需要~~
- ~~多进程harvest推理：已经被RMVPE取代，不需要~~
- ~~crepe的精度支持和RVC-config同步：已经被RMVPE取代，不需要。支持这个还要同步torchcrepe的库，麻烦~~
- 对接F0编辑器
  
  
### 20230528更新
- 增加v2的jupyter notebook，韩文changelog，增加一些环境依赖
- 增加呼吸、清辅音、齿音保护模式
- 支持crepe-full推理
- UVR5人声伴奏分离加上3个去延迟模型和MDX-Net去混响模型，增加HP3人声提取模型
- 索引名称增加版本和实验名称
- 人声伴奏分离、推理批量导出增加音频导出格式选项
- 废弃32k模型的训练

### 20230513更新
- 清除一键包内部老版本runtime内残留的lib.infer_pack和uvr5_pack
- 修复训练集预处理伪多进程的bug
- 增加harvest识别音高可选通过中值滤波削弱哑音现象，可调整中值滤波半径
- 导出音频增加后处理重采样
- 训练n_cpu进程数从"仅调整f0提取"改为"调整数据预处理和f0提取"
- 自动检测logs文件夹下的index路径，提供下拉列表功能
- tab页增加"常见问题解答"（也可参考github-rvc-wiki）
- 相同路径的输入音频推理增加了音高缓存（用途：使用harvest音高提取，整个pipeline会经历漫长且重复的音高提取过程，如果不使用缓存，实验不同音色、索引、音高中值滤波半径参数的用户在第一次测试后的等待结果会非常痛苦）

### 20230514更新
- 音量包络对齐输入混合（可以缓解“输入静音输出小幅度噪声”的问题。如果输入音频背景底噪大则不建议开启，默认不开启（值为1可视为不开启））
- 支持按照指定频率保存提取的小模型（假如你想尝试不同epoch下的推理效果，但是不想保存所有大checkpoint并且每次都要ckpt手工处理提取小模型，这项功能会非常实用）
- 通过设置环境变量解决服务端开了系统全局代理导致浏览器连接错误的问题
- 支持v2预训练模型（目前只公开了40k版本进行测试，另外2个采样率还没有训练完全）
- 推理前限制超过1的过大音量
- 微调数据预处理参数


### 20230409更新
- 修正训练参数，提升显卡平均利用率，A100最高从25%提升至90%左右，V100:50%->90%左右，2060S:60%->85%左右，P40:25%->95%左右，训练速度显著提升
- 修正参数：总batch_size改为每张卡的batch_size
- 修正total_epoch：最大限制100解锁至1000；默认10提升至默认20
- 修复ckpt提取识别是否带音高错误导致推理异常的问题
- 修复分布式训练每个rank都保存一次ckpt的问题
- 特征提取进行nan特征过滤
- 修复静音输入输出随机辅音or噪声的问题（老版模型需要重做训练集重训）

### 20230416更新
- 新增本地实时变声迷你GUI，双击go-realtime-gui.bat启动
- 训练推理均对<50Hz的频段进行滤波过滤
- 训练推理音高提取pyworld最低音高从默认80下降至50,50-80hz间的男声低音不会哑
- WebUI支持根据系统区域变更语言（现支持en_US，ja_JP，zh_CN，zh_HK，zh_SG，zh_TW，不支持的默认en_US）
- 修正部分显卡识别（例如V100-16G识别失败，P4识别失败）

### 20230428更新
- 升级faiss索引设置，速度更快，质量更高
- 取消total_npy依赖，后续分享模型不再需要填写total_npy
- 解锁16系限制。4G显存GPU给到4G的推理设置。
- 修复部分音频格式下UVR5人声伴奏分离的bug
- 实时变声迷你gui增加对非40k与不懈怠音高模型的支持

### 后续计划：
功能：
- 支持多人训练选项卡（至多4人）

底模：
- 收集呼吸wav加入训练集修正呼吸变声电音的问题
- 我们正在训练增加了歌声训练集的底模，未来会公开

### English Translation

**Update on 2023-08-13**
1- General Bug Fixes

- Minimum saving frequency changed to 1, minimum total rounds changed to 2.
- Fixed training errors in models without pretraining.
- Added memory cleanup after separating accompaniment and vocals.
- Changed faiss saving path from absolute to relative.
- Supported paths containing spaces (both for training set paths and experiment names, no longer causing errors).
- Removed mandatory UTF-8 encoding for filelist.
- Resolved high CPU usage issue caused by enabling indexing during real-time voice modulation.

**2- Key Updates**

- Trained the most powerful open-source vocal pitch extraction model RMVPE, used for RVC training, offline/real-time inference, supporting pytorch/onnx/DirectML.
- Supported real-time voice modulation (1), inference (2), and vocal accompaniment separation (3) for A and I series Nvidia GPUs through pytorch-dml. Training switched to CPU training. Supported rmvpe_gpu inference through onnx_dml.

**Update on 2023-06-18**

- Added new pre-trained models for 32k and 48k.
- Fixed inference errors for non-f0 models.
- Optimized index building for training sets longer than an hour, using automatic kmeans feature reduction for faster training, joining, and querying.
- Provided a repository for vocal-to-guitar conversion.
- Removed outliers in data processing.
- Added options tab for onnx export.

**Failed Experiments:**

- Adding temporal dimension to feature retrieval: Unsuccessful, no significant effect.
- Adding optional PCAR dimension reduction to feature retrieval: Unsuccessful, data reduction via kmeans proved more efficient.
- Supported onnx inference (compressed package included): Unsuccessful, pytorch still required for generating nsf.
- Random augmentation in pitch, gender, eq, and noise during training: Unsuccessful, no significant effect.
- Explored integration of small-scale vocoders: Resulted in worse performance.

**To-Do List:**

- Support crepe for pitch recognition in the training set: Replaced by RMVPE, not needed.
- Multi-process harvest inference: Replaced by RMVPE, not needed.
- Synchronize crepe accuracy with RVC-config: Replaced by RMVPE, not needed. Requires syncing with torchcrepe library, troublesome.
- Integrate F0 editor.

**Update on 2023-05-28**

- Added jupyter notebook for v2, Korean changelog, additional environment dependencies.
- Added modes for breath, clear consonant, and fricative protection.
- Supported crepe-full inference.
- UVR5 vocal accompaniment separation with 3 delay removal models and MDX-Net dereverberation model, added HP3 vocal extraction model.
- Added version and experiment name to index names.
- Increased audio export format options for batch export of vocal accompaniment separation and inference.

**Update on 2023-05-13**

- Removed remnants of old versions' runtime from one-click package.
- Fixed bug in training set preprocessing pseudomultiprocessing.
- Added option for pitch recognition via median filtering to mitigate breathy voice phenomenon.
- Added post-processing resampling for exported audio.
- Changed number of CPU processes from "adjust F0 extraction only" to "adjust data preprocessing and F0 extraction".
- Automatically detected index path in logs folder with dropdown list functionality.
- Added "Frequently Asked Questions" tab (also available on GitHub-RVC-wiki).
- Added pitch cache for same-path input audio inference (useful for users testing different timbres, indexes, and median filtering radii, avoiding redundant pitch extraction).

Please note that some sections, particularly involving technical terms and software components, may be challenging to translate precisely without more context.
