## LEfseで細菌叢解析

Author:Tomoya Tsukimi  
Last update:2019-01-14  
※ Galaxyを利用します。

0. インプット用データの準備
QIIME1.8.0で出力されたデータをlefse用に加工する
用いたデータは[Mishima, et al., *American Journal of Physiology-Renal Physiology*, 2017.](https://www.ncbi.nlm.nih.gov/pubmed/29167170)からマウス回盲部の腸内細菌叢データ（属レベル）のRF+vehicleとRF+Cana群（論文ではもう1群あるが3群では有意差出なかったので）。
菌種名の「;」を「|」に変更。サンプル名は削除しグループ分けをclass行として記載。
※LEfSeの仕様か[]はアンダーバーと認識される。例えば「g__[Prevotella]」はlefseでプロットされると「g___Prevotella_」と表示されてしまう。
![](/image/入力ファイル.png)



1. [Galaxy](http://huttenhower.sph.harvard.edu/galaxy/)にアクセスし、LEfSeを検索。
![](/image/Galaxy.png)

2. 「A) Format Data for LEfSe」をクリックし、ファイルをアップロードする。ドラッグ&ドロップすればいいtypeはGenomeは特に変更しなくても良い。Startをクリック。成功するとHistoryにアップロードしたファイル名が表示される。
![](/image/データアップロード.png)
![](/image/データアップロード2.png)
![](/image/データアップロード3.png)
![](/image/データアップロード4.png)

ファイルは[公式](https://bitbucket.org/biobakery/biobakery/wiki/lefse)に配布されている[デモ用txtファイル](https://bitbucket.org/biobakery/biobakery/raw/tip/demos/biobakery_demos/data/lefse/input/hmp_small_aerobiosis.txt)を使用します。

3. 「A) Format Data for LEfSe」をクリック  
メタデータが記載されている向き、グループとして使用するデータ、サンプルごとにノーマライゼーションするかを選択してExcecuteをクリック。
![](/image/A.png)
![](/image/A2.png)

4. 「B) LDA Effect Size (LEfSe)」をクリック
先ほどAを実行したファイルを選択し、Executeをクリック(パラメータはとりあえずデフォルトで良い)。
![](/image/B2.png)


5. C) Plot LEfSe Resultsをクリック
先ほどのBで実行されたファイルを選択し、Executeをクリック
残りのパラメータはとりあえずデフォルトで良い。
菌種名が入りきらないときは、Set text and label options (font size, abbreviations, ...)をAdvancedにし、Label font sizeを小さくすればいい(デフォルトは7)
LDA Scoreの算出方法の詳細は原著論文(https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3218848/)をご参照ください。
概要としてはKruskal-Wallis検定およびWolcoxonの順位和検定を全てに変数（この場合は細菌種）に実施し、有意となった変数を用いて 線形判別分析 (Linear Discriminant Analysis)を行います。 通常の統計検定に加えてグループ間の差異も考慮できることが強みです。ですので、著者たちはbiomarkerの探索に有効ですと主張しています。
![](/image/C2.png)
![](/image/C3.png)

6. D) Plot Cladogram
B) LDA Effect Size (LEfSe)で生成されたファイルを選択。
残りのパラメータはとりあえずデフォルトで良い。ノードの大きさはその菌種の存在量、色が濃いほどグループ間の差が大きいことを示します。個人的にはこの図がLEfSeを使用するメリットかと思います。webツールで存在量とグループを考慮したcladegramを書けるツールはそう多くないので。
![](/image/D2.png)
![](/image/D3.png)
