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

### その他機能
- Inner Mask Threshold
  - 入力画像から内部/外部マスクを生成する際のComponent毎の閾値を指定.
  - それぞれのComponentがこの数値よりも大きい場合に内部テクセルとして扱われる.
    - 例: {0.5, 0.1, 0.1} -> 「Rチャンネルが0.5より大きい」「Gチャンネルが0.1より大きい」「Bチャンネルが0.1より大きい」 の何れかを満たすテクセルは内部テクセルとして処理される.
- Inner Mask Ignore R
  - 入力画像から内部/外部マスクを生成する際に指定したComponentを無視する.
  - Trueの場合Rチャンネルに関して内部/外部判定から除外する.
- Inner Mask Ignore G
  - 同上. Gチャンネルを無視する.
- Inner Mask Ignore B
  - 同上. Bチャンネルを無視する.


## 入力データ
入力テクスチャには縦横サイズが2の冪乗で正方形のものを指定します.

SDFの元としての二値化は, Inner Mask Threshold や Inner Mask Ignore R 等の設定を元に決定されます.
デフォルトでは完全な黒が外部, そうでない場所が内部という扱いでSDFが計算されます.

<img src="img/img_generated_sdf_src.png" width="300">

## 出力データ
Texture Render Target 2D を指定します.

フォーマットはRGコンポーネントがあり, float形式のものを要求します.

例: RGBA16F

結果のSDFは

- Rチャンネル : 幅と高さの大きい方で正規化された距離

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

## メモ
二の冪乗かつ正方形サイズ以外では正しく動作しないかもしれませんが対応検討中です

