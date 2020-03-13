# 使用 ffpmeg 合并视频

1. 将视频格式为 ts 格式

   ```shell
   ffmpeg -i ${base_dir}${file} -vcodec copy -acodec copy -vbsf h264_mp4toannexb ${mov_dir}${filename}
   ```

2. 编辑转化的视频配置文件

   ```txt
   file '文件地址'
   ```
   
3. 使用 ffmpeg 合并视频

   ```shell
   ffmpeg -safe 0 -f concat -i ./tt.txt -c copy 2020年春节离家-bj地段.mp4
   ```

   