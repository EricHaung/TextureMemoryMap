### Unity的貼圖在Runtime下生成時產生的記憶體。 ###
  出於效能測量需求，簡單的紀錄一下Unity在執行時下載貼圖後，無壓縮情況下經由計算圖片尺寸可以得到的記憶體使用量推估情形。<br>
  分成兩部分 : 
  * RGB24
  * 其他
  
  我們可以從貼圖的長乘寬乘位元深度得知該貼圖之RawData總量為多少<br>
  例如 : 4096 px * 4096 px 的 RGBA32 貼圖之 RAWDATA 為 4096 * 4096 * 4 byte = 64 MB<br>
  <br>
  當然，若是其他使用壓縮技術像是DXT，ETC，PVRTC等則該數值會小於 64MB，在此不考慮。
  <br>
  
  若是將無壓縮版本之貼圖使用之空間定義成 1 ，則其使用到之記憶體如下表
  
<table align = "center">
    <tr align = "center">
        <td></td>
        <td>NOT RGB24 </td>
        <td>RGB24</td>
    </tr>
    <tr align = "center">
        <td></td>
         <th colspan="2">In Unity Inspector</th>
    </tr>
    <tr align = "center">
        <td>raw</td>
        <td>1</td>
        <td>1</td>
    </tr>
    <tr align = "center">
        <td>with mip</td>
        <td>4/3</td>
        <td>4/3</td>
    </tr>
    <tr align = "center">
        <td></td>
         <th colspan="2">MEMORY USAGE</th>
    </tr>
    <tr align = "center">
        <td>with mip</td>
        <td>8/3</td>
        <td>(8/3 + 1/3) + 0.111</td>
    </tr>
    <tr align = "center">
        <td>no mip</td>
        <td>6/3</td>
        <td>(6/3 + 1/3)</td>
    </tr>
    <tr align = "center">
        <td>mip</td>
        <td>2/3</td>
        <td>2/3 + 0.111</td>
    </tr>
</table>
<br>
上述數值為測試之後之推估數值，為粗略結果並非精準值。<br>
<br>
RGB24之所以如此奇怪推論應於<a href="https://docs.unity3d.com/ScriptReference/TextureFormat.RGB24.html">官方對RGB24之解釋</a>有關
該敘述提到 :<br>
> there are almost no GPUs that support this format natively, so at texture load time it is converted into an RGBA32 format. <br>
因此可能額外的記憶體使用與此有關。<br>
<br>
最後補述，我們可以使用 UnityEngine.Profiling 命名空間下之 <a href="https://docs.unity3d.com/ScriptReference/Profiling.Profiler.GetRuntimeMemorySizeLong.html">Profiler.GetRuntimeMemorySizeLong(Object o)</a>取得該物件占用之記憶體空間，
然而其限制使用於開發版本以及編輯器下，並有支援類型之限制。
<br>
另外，進入Unity後記憶體變成兩倍和貼圖本身之允許讀寫之設定是否為真有關，然而由於runtime動態載入貼圖必定允許讀寫，因此無法從這裡下手減輕記憶體負擔。<br>
<br>
To Do : <br>
https://github.com/wolfpld/etcpak  Runtime壓縮貼圖成ETC之演算法，找機會來研究看看<br>


