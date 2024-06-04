# 适配器模式

* ## 描述

    > [?] 适配器模式（Adapter Pattern）是作为两个不兼容的接口之间的桥梁。这种类型的设计模式属于结构型模式，它结合了两个独立接口的功能。
    <br><br>这种模式涉及到一个单一的类，该类负责加入独立的或不兼容的接口功能。举个真实的例子，读卡器是作为内存卡和笔记本之间的适配器。您将内存卡插入读卡器，再将读卡器插入笔记本，这样就可以通过笔记本来读取内存卡。
    <br><br>我们通过下面的实例来演示适配器模式的使用。其中，音频播放器设备只能播放 mp3 文件，通过使用一个更高级的音频播放器来播放 vlc 和 mp4 文件。

* ## UML

* ## 代码实现

    ```java
    public class Main {
        public static void main(String[] args) {

            AudioPlayer audioPlayer = new AudioPlayer();

            audioPlayer.play("mp3", "beyond the horizon.mp3");
            audioPlayer.play("mp4", "alone.mp4");
            audioPlayer.play("vlc", "far far away.vlc");
            audioPlayer.play("avi", "mind me.avi");
        }
    }
    ```

    <!-- tabs:start -->
    ### **AudioPlayer.java**
    ```java
    public class AudioPlayer implements MediaPlayer {
        MediaAdapter mediaAdapter;

        @Override
        public void play(String audioType, String fileName) {

            //播放 mp3 音乐文件的内置支持
            if(audioType.equalsIgnoreCase("mp3")){
                System.out.println("Playing mp3 file. Name: "+ fileName);
            }
            //mediaAdapter 提供了播放其他文件格式的支持
            else if(audioType.equalsIgnoreCase("vlc")
                    || audioType.equalsIgnoreCase("mp4")){
                mediaAdapter = new MediaAdapter(audioType);
                mediaAdapter.play(audioType, fileName);
            }
            else{
                System.out.println("Invalid media. "+
                        audioType + " format not supported");
            }
        }
    }
    ```
    ### **MediaPlayer.java**
    ```java
    public interface MediaPlayer {
        public void play(String audioType, String fileName);
    }
    ```
    <!-- tabs:end -->

    <!-- tabs:start -->
    ### **MediaAdapter.java**
    ```java
    public class MediaAdapter implements MediaPlayer {

        AdvancedMediaPlayer advancedMusicPlayer;

        public MediaAdapter(String audioType){
            if(audioType.equalsIgnoreCase("vlc") ){
                advancedMusicPlayer = new VlcPlayer();
            } else if (audioType.equalsIgnoreCase("mp4")){
                advancedMusicPlayer = new Mp4Player();
            }
        }

        @Override
        public void play(String audioType, String fileName) {
            if(audioType.equalsIgnoreCase("vlc")){
                advancedMusicPlayer.playVlc(fileName);
            }else if(audioType.equalsIgnoreCase("mp4")){
                advancedMusicPlayer.playMp4(fileName);
            }
        }
    }
    ```
    ### **Mp4Player.java**
    ```java
    public class Mp4Player implements AdvancedMediaPlayer{

        @Override
        public void playVlc(String fileName) {
            //什么也不做
        }

        @Override
        public void playMp4(String fileName) {
            System.out.println("Playing mp4 file. Name: "+ fileName);
        }
    }
    ```
    ### **VlcPlayer.java**
    ```java
    public class VlcPlayer implements AdvancedMediaPlayer{
        @Override
        public void playVlc(String fileName) {
            System.out.println("Playing vlc file. Name: "+ fileName);
        }

        @Override
        public void playMp4(String fileName) {
            //什么也不做
        }
    }
    ```
    ### **AdvancedMediaPlayer.java**
    ```java
    public interface AdvancedMediaPlayer {
        public void playVlc(String fileName);
        public void playMp4(String fileName);
    }
    ```
    <!-- tabs:end -->

    ![](/.images/doc/advance/design-pattern/dp-adapter-01.png ':size=80%')

* ## Reference

    + https://www.runoob.com/design-pattern/adapter-pattern.html
