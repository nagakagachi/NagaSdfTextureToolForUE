# SDF Generator for Unreal Engine
マスク画像からSDF画像を生成するUEの Editor Utility Widget　ツールです.

UEマテリアルを利用して Jump Flooding アルゴリズムをGPUで実行しています.

</br>

## 使い方
Editor Utility Widget である EUW_GenerateSDF をコンテキストメニューから実行

Src Texture 2D に入力となるTexture2Dを指定.

Dst Render Target に結果のSDFを出力するTexture Render Target 2Dを指定.

Generateボタンを押すとSDFが計算されます.

<img src="img/img_widget.png" width="512">

## 入力データ
入力テクスチャには縦横サイズが2の冪乗で正方形のものを指定します.

テクセルが黒(RGB=0,0,0)の場合は外部, 黒ではない場合は内部としてSDFが計算されます.

<img src="img/img_generated_sdf_src.png" width="300">

## 出力データ
Texture Render Target 2D を指定します.

フォーマットはRGコンポーネントがあり, float形式のものを要求します.

例: RGBA16F

結果のSDFは

- Rチャンネル : UV空間での境界までの最短距離

- Gチャンネル : 内部テクセルの場合に非ゼロ

となります.

<img src="img/img_generated_sdf_rg.png" width="300">
<p>
<img src="img/img_generated_sdf_r.png" width="300"><img src="img/img_generated_sdf_g.png" width="300">
</p>

## SDFテクスチャの保存
Render Target 2Dは上書きされてしまうため右クリックから

「スタティックテクスチャを作成」

で保存することをおすすめします.

<img src="img/img_save_static_texture.png" width="300">


## Implementation Detail
Jump FloodingをUEマテリアルで実装することでGPU実行による高速化を意図しています.

マテリアルは M_JumpFlood_Itr_Signed 等が該当します.

Jump Floodingのセットアップや反復処理は BPFL_GenerateSDF に実装されています.

<img src="img/img_material.png" width="500">


