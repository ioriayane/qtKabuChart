# qtKabuChart
Chart program for japanese stocks only

## Description
This source code is test alpha version of Qt widget application created by open-source(LGPL) Qt5.5.1.  
Using this program, you can display candlestick chart of japanese stocks.  

株チャートを表示するための評価版プログラムです。  
マックとUbuntuで個人的利用するために、LGPL版のQt5.5.1で作成したc++プログラムソースです。    

## Caution and Limitation
I am a beginner for qt and c++.  
There is a possibility of serious trouble occurs.  
This program is for japanese stocks only.

重大なバグが存在する可能性は否定できません。  
日本株限定で、株価データを[http://k-db.com](http://k-db.com)から取得してチャート表示をします。  
データ取得元k-db.comのアクセス制限によりデータ取得ができない場合があります。  
OsX(El Capitan)とUbuntu14.04でデバッグしましたが、Windows環境では充分チェックしていません。

## Demo
![](https://github.com/narih/qtKabuChart/wiki/images/sosei.png)  

## Requirement
- [Open source Qt5.x](https://www.qt.io/download-open-source/  "ダウンロードページ") 
- [Ta-Lib](http://ta-lib.org/) open-source(BSD license) for c/c++  
Even if Ta-Lib library is not installed, I think error will not be occur.  
But, technical indicators not visible like MA.  
Ta-Libがない場合でもエラーは出ないはずですが、大部分のテクニカル指標が表示できません。  

##Feature
- Show candlestick or line chart
- Change brand by selecting tree node (or input code from keyboard)  
- Change day,week,month time-series by combobox
- Change display period by editing month textbox
- Toggle display volume, price information, date select scale line
- Display indicator Ichimoku, Heikin ashi
- With Ta-Lib, you can display MA(x4), Bollinger band, Parabolic
- With Ta-Lib, you can display oscillator MACD, RSI, Momentum, ATR
- Change colors of chart elements by color dialog

## Usage
Qt Creatorで「プロジェクトを開く」からqtKabuChart.proを開きます。  
「プロジェクトの設定」ボタンを押し、時間をおいて左下三角形ボタンがグレーから緑に変化して実行可能です。  
実行ボタンを押して、初回実行時のみデータフォルダを設定するためのフォルダ選択ダイアログが表示されます。  
指定したデータフォルダに1000件以上の銘柄個別ファイルが作成されますので注意してください。  
フォルダに0101ファイル（日経平均）が存在すればこれを読み込んで表示します。  
ファイルが存在しなければ、k-db.comにアクセスしてデータをダウンロードします。  
まず日経平均の１年分データを取得し、0101ファイルを作成してチャートが表示されます。  
引き続き指標と個別銘柄の最新日付１日分データのみがダウンロードされます。  
個別銘柄の取得により、sqliteデータベースの銘柄テーブルが更新されます。   これでツリーウィジェットの銘柄グループの展開ができるようになり、銘柄の変更ができます。  
日経平均以外の銘柄を表示する時に、データが10日以下の場合は随時１年分データを取得して更新します。  

実行後親フォルダに「build-qtKabuChart-Desktop_Qt_5_5_1_clang_64bit-Debug」が作成されます。  
ディレクトリ名は環境で多少異なると思いますが、この中に実行ファイルがあるはずです。  
ubuntuではこの中に実行ファイルとデータベース「sqlite.db」設定ファイル「myChart.ini」があります。  
ただし、マックではFinderからは「qtKabuChart.app」が隠しフォルダになっており、  
「build-qtKabuChart-Desktop_Qt_5_5_1_clang_64bit-Debug/qtKabuChart.app/Contents/MacOS」  
に各ファイルがあるようです。  
デバッグ以降は、ターミナルからこれを直接実行してもチャート表示可能です。  
ただし、１回目はQtCreatorの時と同じようにディレクトリを聞いてくるはずです。  
さらに、ターミナルにqDebug()の情報が表示されるため、遅い場合もあるかもしれません。  
また、リリースビルドはまだよくわかりません。ググって調べてみてください。

usage.htmlに使い方などについての説明を記載しましたので参照してください。
####usage.htmlを「hint」タブページで参照する方法
Qt Creatorのプロジェクトタブのリソースフォルダに「hint.rc」を見つけてコンテキストメニューを表示します。  
hint.qrcにprefix "/" とusage.htmlを追加してください（下図参照）。  
![](https://github.com/narih/qtKabuChart/wiki/images/add_resource.png)
これでプロジェクトを実行すると、「hint」タブページでhtmlファイルが参照できると思います。

## Known Issues
- I don't know how to include the Ta-Lib library under Windows environment.  
UbuntuのVirtualBox（Windows10）のQt5.6.0MSVC2013 64bit環境で、プロジェクトに  
SOURCES += fortalib.cpp  
HEADERS  += fortalib.h  
LIBS += c:/ta-lib/c/lib/ta_libc_cdd.lib  
INCLUDEPATH += c:/ta-lib/c/include  
を追加しても、Ta-Lib内の関数見つからないとのエラーが発生してビルドできませんでした。  
解決策わかる人がいれば教えてください。
- I can not resize the QDockWidget by reading saved Settings.  
終了時QSettingsにQDockWidgetのサイズを保存しているが、初期化で反映されません。
- There is a case that I can not display the chart.  
銘柄変更の繰り返しでチャート表示部が白くなり、チャート表示されなくなってしまうことがあります。  
以前は、QGraphicsViewのQGraphicsSceneを銘柄変更時にclear()していました。  
この時は、数十銘柄の表示でチャートが表示できなくなってしまうことが多発しました。  
Macのアクティビティモニタでプロセスのメモリ推移を監視してみましたが、大きな変動はなさそうでした。  
現在は、QGraphicsViewのQGraphicsSceneにbaseRectangleを作成し、背景としてクリアせずに利用しています。  
このbaseにQGraphicsItemをchildとして追加し、銘柄変更時にこれらchildのみをクリアする仕様にしています。  
これで、以前のようにチャート表示ができなくなることはなくなったかもしれません。  

## Reference
see usage.html  

## License
MIT see LICENSE
