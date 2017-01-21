# libgdx-hiero-bitmapfont: ビットマップフォントを使用したlibGDXゲームプロジェクトでのテキストの表示

## ビットマップフォントの作成
<a href="https://github.com/kojima/libgdx-hiero-bitmapfont/wiki/Hiero---%E3%83%93%E3%83%83%E3%83%88%E3%83%9E%E3%83%83%E3%83%97%E3%83%95%E3%82%A9%E3%83%B3%E3%83%88%E3%83%91%E3%83%83%E3%82%B1%E3%83%BC%E3%82%B8%E3%83%84%E3%83%BC%E3%83%AB%E3%81%AE%E4%BD%BF%E3%81%84%E6%96%B9">Wikiページ</a>を参考に、ゲームプロジェクトで使用するビットマップフォントを作成します。

## ビットマップフォントの使用
libGDXのメインコードを以下のようにプログラムすると、スコアテキストが画面左上に表示され、画面をタップする毎にスコアが1点加算されます:<br/>
<img src="https://github.com/kojima/libgdx-hiero-bitmapfont/blob/master/screenshots/game_screen1.png?raw=true" width="320px"/>&nbsp;&nbsp;-(タップ x 2)→&nbsp;&nbsp;<img src="https://github.com/kojima/libgdx-hiero-bitmapfont/blob/master/screenshots/game_screen2.png?raw=true" width="320px" />
``` java
package com.hide.hiero;

import com.badlogic.gdx.ApplicationAdapter;
import com.badlogic.gdx.Gdx;
import com.badlogic.gdx.graphics.GL20;
import com.badlogic.gdx.graphics.Texture;
import com.badlogic.gdx.graphics.g2d.Batch;
import com.badlogic.gdx.graphics.g2d.BitmapFont;
import com.badlogic.gdx.graphics.g2d.SpriteBatch;
import com.badlogic.gdx.scenes.scene2d.Actor;
import com.badlogic.gdx.scenes.scene2d.InputEvent;
import com.badlogic.gdx.scenes.scene2d.InputListener;
import com.badlogic.gdx.scenes.scene2d.Stage;
import com.badlogic.gdx.utils.viewport.FitViewport;

public class HieroBitmapFont extends ApplicationAdapter {

	// スコア表示用のクラスを定義する
	//   Actorクラスを継承する
	//   Hieroで作成したビットマップフォントを読み込み、
	//   そのフォントを使ってBatchにtext変数中のテキストを書き込む
	private static final class ScoreText extends Actor {
		String text = "";
		BitmapFont font = new BitmapFont(Gdx.files.internal("meiryo_ui.fnt"));

		@Override
		public void draw(Batch batch, float parentAlpha) {
			super.draw(batch, parentAlpha);
			font.draw(batch, text, getX(), getY());
		}
	}

	private Stage stage;					// ゲームで使用するステージ
	private ScoreText scoreText;			// スコアを表示するためのActor
	private Integer currentScore = 0;		// 現在のスコア

	@Override
	public void create () {
		stage = new Stage(new FitViewport(1776, 1080));     // ゲーム用のステージを1776x1080のサイズで作成
		Gdx.input.setInputProcessor(stage);                 // ステージでインプット(タッチ入力など)を処理する
		// ステージ用のイベントリスナを定義する
		InputListener inputListener = new InputListener() {
			public boolean touchDown (InputEvent event, float x, float y, int pointer, int button) {
				return true;
			}

			// タッチアップ(タッチして指を離したタイミング)で得点を1点加算する
			// 加算したスコアを用いて、スコアテキストを更新する
			// ※ scoreTextのtext変数の値を変えておきさえすれば、render()メソッド中の
			//    stage.draw()メソッド内で、scoreTextのdraw()メソッドが呼び出されて表示が更新される
			public void touchUp (InputEvent event, float x, float y, int pointer, int button) {
				currentScore++;
				scoreText.text = "スコア: " + currentScore + "点";
			}
		};
		// ステージでタッチ入力を処理するためのリスナー(listener)を追加する
		stage.addListener(inputListener);

		// スコアテキストを生成し、ステージ左上に表示する
		scoreText = new ScoreText();
		scoreText.text = "スコア: " + currentScore + "点";
		scoreText.setPosition(32, stage.getHeight() - 40);
		stage.addActor(scoreText);
	}

	@Override
	public void render () {
		// 画面をミッドナイトブルー(red = 44, green = 62, blue = 80)に設定する
		// 色参照: https://flatuicolors.com/
		Gdx.gl.glClearColor(44 / 255.f, 62 / 255.f, 80 / 255.f, 1);
		Gdx.gl.glClear(GL20.GL_COLOR_BUFFER_BIT);   // 画面をクリアする
		stage.act(Gdx.graphics.getDeltaTime());     // ステージの状態を前回render呼び出しからの経過時間(delta time)分だけ更新する
		stage.draw();                               // ステージを最新の状態に描画する
	}

	@Override
	public void dispose () {
		stage.dispose();		// ステージを破棄する
	}
}
```
