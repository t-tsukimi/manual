## LEfseで細菌叢解析（Galaxyを利用）

Author:Tomoya Tsukimi  
Last update:2019-04-23  


### 0. インプット用データの準備
---

QIIME1.8.0で出力された組成データをLEfSe用に加工します。  
使用したのは[Mishima, et al., 2017.](https://www.ncbi.nlm.nih.gov/pubmed/29167170)からRF+vehicleとRF+Cana群のマウス回盲部細菌叢データ（属レベル）です。  
※論文ではもう1群ありますが、以降の解析で有意差が出ずにプロットできなかったため、Normal群を削除しています。  
なお、[公式](https://bitbucket.org/biobakery/biobakery/wiki/lefse)で配布されている[デモ用txtファイル](https://bitbucket.org/biobakery/biobakery/raw/tip/demos/biobakery_demos/data/lefse/input/hmp_small_aerobiosis.txt)はなぜか途中でエラーになります。。。

#### 0.a QIIMEのコマンドでLEfSe用データを出力する場合
```sh
#QIIMEが立ち上がった状態で
summarize_taxa.py \
-i otu_table.biom \ # -i:biomファイル
-o summarize_taxa_L6 \ # -o:出力ファイル名
-m mapping_file.txt \ # -m:マッピングファイル
--delimiter '|' # 区切り記号
```
詳細は[こちら](https://twbattaglia.gitbooks.io/introduction-to-qiime/content/lefse.html)のページを参照してください。  
このQIIMEコマンドで出力すると各菌のレベルの合計値も算出されます。
![](https://github.com/t-tsukimi/manual/blob/master/LEfSe/image/input_qiime_command.png)

#### 0.b その他の組成データを使用する場合
菌レベルの区切りを「|」に変更し(例えばgreen genesでは菌のレベルは「;」で区切られているので、「|」に置換します)、サンプル名を削除してグループ分けをclass行として記載します。  
※LEfSeの仕様か[]はアンダーバーと認識されます。例えば「g__[Prevotella]」は「g___Prevotella_」と表示されます。
![](https://github.com/t-tsukimi/manual/blob/master/LEfSe/image/%E5%85%A5%E5%8A%9B%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB.png)


#### ※abどちらの場合でも動作しますが、LefSeのデモファイルから察するにLEfSe自体は0.a形式を前提にしているように思います。
<br>

### 1. input用データのアップロード  
---
[Galaxy](http://huttenhower.sph.harvard.edu/galaxy/)にアクセスし、LEfSeを検索します。  
左サイドバーの右上のアイコンをクリックして、0.で準備したデータをドラッグ&ドロップします。Startをクリックし、成功すると右サイドバーのHistoryにアップロードしたファイル名が表示されます。
![](https://github.com/t-tsukimi/manual/blob/master/LEfSe/image/Galaxy.png)
![](https://github.com/t-tsukimi/manual/blob/master/LEfSe/image/%E3%83%87%E3%83%BC%E3%82%BF%E3%82%A2%E3%83%83%E3%83%97%E3%83%AD%E3%83%BC%E3%83%89.png)
![](https://github.com/t-tsukimi/manual/blob/master/LEfSe/image/%E3%83%87%E3%83%BC%E3%82%BF%E3%82%A2%E3%83%83%E3%83%97%E3%83%AD%E3%83%BC%E3%83%892.png)
![](https://github.com/t-tsukimi/manual/blob/master/LEfSe/image/%E3%83%87%E3%83%BC%E3%82%BF%E3%82%A2%E3%83%83%E3%83%97%E3%83%AD%E3%83%BC%E3%83%893.png)
![](https://github.com/t-tsukimi/manual/blob/master/LEfSe/image/%E3%83%87%E3%83%BC%E3%82%BF%E3%82%A2%E3%83%83%E3%83%97%E3%83%AD%E3%83%BC%E3%83%894.png)  
<br>


### 2. LEfSe用フォーマットに変換  
---
「A) Format Data for LEfSe」をクリックし、メタデータが記載されている向き、グループとして使用するデータ、サンプルごとにノーマライゼーションするかを選択してExcecuteをクリック。
![](https://github.com/t-tsukimi/manual/blob/master/LEfSe/image/A.png)
![](https://github.com/t-tsukimi/manual/blob/master/LEfSe/image/A2-2.png)  
<br>

### 3. LDA Effect Sizeを求める
---
「B) LDA Effect Size (LEfSe)」をクリックし、先ほどAを実行したファイルを選択してExecuteをクリック(パラメータはとりあえずデフォルトで良いです)。
![](https://github.com/t-tsukimi/manual/blob/master/LEfSe/image/B2.png)

LDA Scoreの算出方法の詳細は[原著論文](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3218848/)をご参照ください。  
概要としてはKruskal-Wallis検定およびWolcoxonの順位和検定を全変数（この場合は細菌種）に実施し、有意となった変数を用いて線形判別分析 (Linear Discriminant Analysis)を行います。  
通常の統計検定に加えてグループ間の差異も考慮できることが強みです（たぶん）。そのため、著者たちはbiomarkerの探索に有効であると主張しています。  
線形判別分析については明治大学データ化学工学研究室(金子研究室)の[資料](https://datachemeng.com/lineardiscriminantanalysis/)が参考になります。  
<br>



### 4. 結果をプロットする
---
「C) Plot LEfSe Results」をクリックし、先ほどBで実行されたファイルを選択してExecuteをクリック。  
成功するとブラウザ上で確認できるほか、pngファイルをダウンロードすることも可能です。  
※菌種名が入りきらないときは、「Set text and label options (font size, abbreviations, ...)」をAdvancedにし、Label font sizeを小さくする(デフォルトは7）。
![](https://github.com/t-tsukimi/manual/blob/master/LEfSe/image/C2.png)
![](https://github.com/t-tsukimi/manual/blob/master/LEfSe/image/C3.png)  
<br>


### 5. Cladogramをプロットする
---
「D) Plot Cladogram」をクリックし、先ほどBで実行されたファイルを選択してExecuteをクリック（残りのパラメータはとりあえずデフォルトで良い）。  
成功するとブラウザ上で確認できるほか、pngファイルをダウンロードすることも可能です。  

ノードの大きさはその菌種の存在量、色が濃いほどグループ間の差が大きいことを示します。  
個人的にはこの図がLEfSeを使用する最も大きなメリットかと思います。  
webツールで存在量とグループを考慮したCladegramを書けるのは便利です。
![](https://github.com/t-tsukimi/manual/blob/master/LEfSe/image/D2.png)
![](https://github.com/t-tsukimi/manual/blob/master/LEfSe/image/D3.png)
