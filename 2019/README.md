# 2019

1. 对于产品弄得不太清楚的逻辑，帮助其通过脑图列出所有的可能性，留下代码逻辑+产品逻辑，更好的为他人理解代码理解需求，也节约了更多的沟通时间
2. 对于代码中出现的更改，及时添加注释，方便他人和自己在未来再次改版的时候能快速的理清思路 eg:

   ```swift
   // 免费未参加最后一个视频，如果只有一个视频正常处理
                          if (playerItem.hasAlreadyPlayedToLastVideo) {
                              controlView.coursePlayerStatus = HMCoursePlayerStatusLastPlayItem;
                              // 这里将其置为-1的原因: 2019.5月版更新：播放到最后一节课也会出现继续学习按钮，此时点击继续学习按钮的操作逻辑是：开始播放课程的第一小节，所以会调用[self playNextExcellentCourse] 方法，-1的next可播放item就是0，也就是第一小节（下面同理）
                              playerItem.currentPlayingVideo.index = -1;
                          } else {
                              controlView.coursePlayerStatus = HMCoursePlayerStatusTryWatchEnd;
                          }
   ```

