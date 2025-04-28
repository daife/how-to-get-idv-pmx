[中文版 (Chinese Version)](README.md)

# how-to-get-idv-pmx

## Directory Structure / 目录结构

```
how-to-get-idv-pmx/
├── README.md
├── README_En.md

```

Please put example images for each step in the `images/` folder and reference them in the documentation.

---

## English Guide(Translate from Chinese documents by ai,there may be inaccuracies)

### Identity V Model Extraction + MMD Animation Workflow

#### Introduction
Animating your favorite game characters is fun, but NetEase's unique npk encryption makes extraction very difficult. After many attempts and pitfalls, here is a complete workflow.

**Warning!!!**: This README is not fully polished. The author wrote it while working through the process, so there may be misleading or inappropriate parts. If you want to get pmx models according to this tutorial, please make sure you read the entire README and always know what you are doing.

#### Steps

1. **Obtain npk files**  
   *Android version*:

       CN: /storage/emulated/0/Android/data/com.netease.dwrg/files/netease/dwrg.common/ 
       Global: /storage/emulated/0/Android/data/com.netease.idv.googleplay/files/netease/dwrg.common/
   *PC version*: wherever you installed it
    
    Related file paths (personal opinion):
    ```
    dwrg2/
    |-Documents/
    |  |-res/ (temporary resources for each game update)
    |  |-res_reserved/ (resources reorganized after updates)
    |
    |-res/ (basic game resources, usually unchanged after installation)
    ```

2. **Set up Python environment**  
   It is recommended to use conda or other virtual environments. Tutorials are widely available.

3. **Clone GitHub project**  
   You need to install git. There are many tutorials online.
   Then clone the following project:
   ```
   git clone https://github.com/MarcosVLl2/NeoXtractor.git # A great project, based on decryption rules found in China, and still maintained (while most Chinese npk unpacking projects have stopped updating)
   ```

4. **Install dependencies**  
   Run `pip install -r requirements.txt`

5. **Unpack npk**  
   Run `main.py` and follow the instructions at https://github.com/MarcosVLl2/NeoXtractor to unpack, and put the unpacked content in a separate folder.

6. **Find model and texture files**  
   Use NeoXtractor's mesh view to open the folder and search visually.
   `.mesh` files are models, `.dds` files are textures.
   
   ![Model and texture example](images/step6_mesh_dds.png)

7. **Export pmx model**  
   The exported pmx bone names may have issues. It is recommended to rebind bones and rigid bodies later. Doing it yourself makes the model more standard and helps you learn more.

8. **Install Blender and mmd_tools plugin**  
   I use Blender 4.3.2 and mmd_tools 4.2.2.

9. **Import and process pmx**  
   Separate the model by material, then select the bones in object mode and delete them, also delete joints and rigid bodies (they are empty anyway).

10. **Semi-automatic bone generation and auto skinning**  
    Use AutoRig Pro and voxel skinning plugin to create a basic skeleton and export as FBX.
    Reference video: [🦴 Auto Rig (ARP: Auto-Rig Pro 3.74.40 Chinese-English) Blender plugin free download](https://www.bilibili.com/video/BV15VZzYDEVu/?share_source=copy_web&vd_source=403e5319c178de32627fda35d921d9b7)

    Note: After auto-generating bones, you should first edit the reference bones (especially knees, elbows, and fingers), then click "Match to Rig" (the skeleton will disappear and become a rectangle outline, etc.), then select all model parts, then the bones, use the voxel skinning plugin to bind, and export as FBX (not Blender's built-in FBX export, use the plugin's export).

11. **Import FBX and convert to MMD model**  
    Open a new window, import the FBX (some say to select "force connect children" and "auto bone orientation", but in my test, enabling them caused issues), then select the model and use mmd_tools' "Convert Model" to convert FBX to pmx and export.

12. **Rename bones**  
    Use pmxeditor to import pmx and rename bones (it seems you can also rename in the previous step). Recommended PE plugin: [PmxEditor plugin: Quickly convert game models to MMD models 2.5](https://www.bilibili.com/video/BV1ZY4y1r72L/?share_source=copy_web&vd_source=403e5319c178de32627fda35d921d9b7)

    (You may notice there is an extra spine bone compared to standard MMD models. Not sure why NetEase does this!)

13. **Verify the result**
    Try moving bones in pose mode to see if the character moves correctly. At this point, you may notice the downside of auto skinning: some parts unrelated to the torso are also weighted (e.g., Lisa's wings stretch when the upper arm moves). If you can repaint weights yourself, feel free to explore further. The author gave up here after finding a faster way.

14. **Eureka moment**
    After the above, you should have a good idea of how pmx works. Open the NeoXtractor directory and modify bone_name.py. NeoXtractor actually provides auto bone renaming, but there are some issues (maybe because it's for the global version and I use CN). The new renaming rules (tested on Gardener Tulip skin):
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
    At this point, you may realize many previous steps were unnecessary!

15. **Get texture files**  
    The best way is to find the desired texture files directly in the resource folder (only the base color texture is needed).
    The author tried exporting UV maps from Blender and using Python scripts to match dds files automatically, but visual algorithms didn't work well. If you achieve this, please let me know!
    So, another way: use RenderDoc to capture a frame in Identity V with your character visible, analyze nodes, and export relevant files (preferably as png). Downside: rare models are hard to get.

16. **Texture processing**
    Always process textures first! Adjust the alpha channel (remove it if you don't need transparency). The author initially tried to fix this in Blender's shader editor, but this caused export issues (textures became transparent in pmx). The reason may be mmd_tools can't save Blender shader settings.

17. **Texture binding**
    (You should have separated the model by material in step 9.) Use UV maps to help bind all parts to the correct textures.

18. **You got it!**
    Now you can import vmd or vpd motions, or export the pmx model.

Unfinished TODO:
1. - [ ] Automate finding dds files by UV map
2. - [ ] Bone physics
3. - [ ] Morph targets
