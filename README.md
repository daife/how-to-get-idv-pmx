[English Version](README_En.md)

# how-to-get-idv-pmx

## 目录结构 / Directory Structure

```
how-to-get-idv-pmx/
├── README.md
├── README_En.md

```

请将每一步对应的示例图片放入 `images/` 文件夹，并在文档中引用。

---

## 中文说明

### 第五人格模型提取 + MMD动画制作一条龙教程

#### 前言
让喜欢的游戏人物能按照自己的想法动起来十分有趣，但网易独特的npk加密导致提取变得十分困难。试过很多方法踩了很多坑，最后走出下面这条完整流程。

警告！！！：README还未完善，这是作者自己边做边写的，所以存在误导、不妥，若要根据此教程获取pmx模型，请确保你先看完整篇README并时刻知道你应该做什么。

#### 步骤

1. **获得npk文件**  
   *安卓版*：

       国服：/storage/emulated/0/Android/data/com.netease.dwrg/files/netease/dwrg.common/ 
       国际服：/storage/emulated/0/Android/data/com.netease.idv.googleplay/files/netease/dwrg.common/
   *电脑版*：安装在哪就在哪
    
    相关文件路径（为个人看法）：
    ```
    dwrg2/
    |-Documents/
    |  |-res/(这是每次游戏更新时的临时资源)
    |  |-res_reserved/(这是更新资源和已有资源重新整合后的资源)
    |
    |-res/(这里存放游戏最基础的资源，一般游戏安装后就不会变了)
    ```

2. **配置Python环境**  
   推荐使用conda等虚拟环境，教程可自行搜索。

3. **克隆GitHub项目**  
   需安装git，相关教程网上很多。
   然后使用git克隆以下项目
   ```
   git clone https://github.com/MarcosVLl2/NeoXtractor.git #确实是一个很好的项目，借鉴了原来国内发现的解密规则，作者现在还保持着更新(虽然国内的几个npk解包项目已经停更很久了)
   ```

4. **安装依赖**  
   运行 `pip install -r requirements.txt`

5. **解包npk**  
   启动 `main.py`，按照https://github.com/MarcosVLl2/NeoXtractor的说明进行解包，并将解包内容放入单独的文件夹。

6. **查找模型与贴图**  
   使用neoxtractor的mesh view功能打开文件夹，进行酣畅淋漓的人眼搜索🤣
   `.mesh`为模型文件，`.dds`为贴图文件。
   
   ![模型与贴图示例](images/step6_mesh_dds.png)

7. **导出pmx模型**  
   
   导出的pmx骨骼命名存在问题，推荐后续自行绑定骨骼与刚体。自己绑骨绑刚体能让模型更加规范化，也能学到更多东西。

8. **Blender与mmd_tools插件安装**  
   我使用的是Blender 4.3.2与mmd_tools 4.2.2。

9.  **导入与处理pmx**  
   将模型按材质分开，然后选中骨骼，在物体模式下delete骨骼，顺便把joints和rigidbodies删了（反正也是空的）。

10. **半自动生成骨骼与自动绑骨**  
    这里使用了AutoRig Pro和体素蒙皮插件，只创建了基本的骨架，导出为FBX。
    这两个插件参考这个b站视频：【🦴 自动绑骨 (ARP：Auto-Rig Pro 3.74.40  中英对照版)Blender中文版插件免费下载】 https://www.bilibili.com/video/BV15VZzYDEVu/?share_source=copy_web&vd_source=403e5319c178de32627fda35d921d9b7

    补充说明：自动生成骨骼后首先应该编辑参考骨骼（把一些部位微调一下，特别是膝关节肘关节以及手指部分）然后点击“装备匹配”（这时候骨架就会消失，变成长方体边框等），然后先全选模型部分后选骨骼，选择体素蒙皮插件并绑定，然后导出为fbx（不是bender自带的导出FBX格式，应该通过插件导出）


11. **FBX导入与MMD模型转化**  
    新开一个窗口，导入fbx（听说要选择强制连接子级和自动骨骼坐标系，但是我实测选了后好像反而出问题），然后选中模型后点击mmd tools的“转化模型”，将fbx转化为pmx并且导出

12. **骨骼重命名**  
    使用pmxeditor，导入pmx并且对骨骼进行重命名（似乎在上一步就能重命名了🧐）这里推荐一个方便的PE插件：【【PmxEditor插件】游戏模型“快速”转化为MMD模型 2.5】 https://www.bilibili.com/video/BV1ZY4y1r72L/?share_source=copy_web&vd_source=403e5319c178de32627fda35d921d9b7

    (还有就是，你会发现这比标准的mmd模型多了一块spine骨，不知道网易这样做的意义是什么🤣)

13. **验证效果**
    可以试着在姿态模式拖动骨骼，查看人物是否会跟随运动。到这一步的时候，你会发现前面自动绑骨的插件的弊端：一些与躯干不相干的部分也被自动添加了权重（比如丽莎背后的翅膀会因大臂运动而拉伸变形😢）
    如果你可以自己重新刷权重，请自行探索后面的步骤，作者到这一步就放弃了，因为作者发现了更快捷的方法🤣


14. **真香定律，悬崖勒马**
    经过前面的探索，作者已经大概知道pmx是如何运作的了。打开我们的neoxtractor目录，自行修改其中的bone_name.py。没错，事实上neoxtractor提供自动化骨骼重命名，但是存在一些问题（我不清楚是不是因为那是为国际服准备的而我使用的是国服），新的重命名规则如下（园丁郁金香皮肤测试效果良好）：
    ```
    biped,センター
    biped_footsteps,
    biped_pelvis,下半身
    biped_l_thigh,左足
    biped_l_calf,左ひざ
    biped_l_foot,左足首
    biped_l_toe0,左つま先
    biped_r_thigh,右足
    biped_r_calf,右ひざ
    biped_r_foot,右足首
    biped_r_toe0,右つま先
    biped_spine,下半身
    biped_spine1,上半身
    biped_spine2,上半身2
    biped_neck,首
    biped_l_clavicle,左肩
    biped_l_upperarm,左腕
    biped_l_forearm,左ひじ
    biped_l_hand,左手首
    biped_l_finger0,左親指１
    biped_l_finger01,左親指２
    biped_l_finger1,左人指１
    biped_l_finger11,左人指２
    biped_l_finger2,左中指１
    biped_l_finger21,左中指２
    biped_r_clavicle,右肩
    biped_r_upperarm,右腕
    biped_r_forearm,右ひじ
    biped_r_hand,右手首
    biped_r_finger0,右親指１
    biped_r_finger01,右親指２
    biped_r_finger1,右人指１
    biped_r_finger11,右人指２
    biped_r_finger2,右中指１
    biped_r_finger21,右中指２
    biped_head,頭

    ```
    至此，你应该发现了前面有很多不必要的步骤🤣
15. **获取贴图文件**  
    最好的方法是直接在资源文件夹里面找到想要的贴图文件（只需要基础色的贴图即可）
    作者尝试首先用blender导出uv图，然后使用python脚本自动化匹配dds，试了很多视觉算法，但是效果都不理想，如果谁实现了自动化匹配请告诉我，谢谢了💕。
    所以作者另辟蹊径了：
    使用RenderDoc打开第五人格，保证界面里包含自己想要的人物模型，然后抓取一帧到RenderDoc里面分析，逐个节点分析，导出相关的文件（最好转化为png格式）。这个方法的缺点就是很难获取稀有的模型（因为你很少在游戏里见到）
16. **贴图处理**
    
    一定要先处理贴图！！！重新调整alpha通道（不需要半透明效果的话直接去除alpha通道即可），作者起初始终在blender的着色编辑器上下功夫，踩了很多坑（这样虽然可行，但是该pmx无法导出，导出会出现贴图变透明的问题，原因可能是mmd tools无法保存blender着色器的设置）

17. **贴图绑定**
    
    （你应该在步骤9就已经将模型按材质分开了吧？）可以利用uv图辅助观察，将所有部分绑定好贴图

18. **You got it!**
    
    现在可以导入各种vmd或者vpd动作了，也可以将pmx导出了

未完成的清单：
1. - [ ] 自动化通过uv图寻找dds文件
2. - [ ] 骨骼物理
3. - [ ] morph形态键