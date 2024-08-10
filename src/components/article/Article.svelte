<script>
	import tailwindConfig from '../../../tailwind.config';
	import resolveConfig from 'tailwindcss/resolveConfig';
	import { base } from '$app/paths';

	// import Youtube from './Youtube.svelte';

	let softmaxEquation = `$$\\text{Softmax}(x_{i}) = \\frac{\\exp(x_i)}{\\sum_j \\exp(x_j)}$$`;
	let reluEquation = `$$\\text{ReLU}(x) = \\max(0,x)$$`;

	let currentPlayer;

	const { theme } = resolveConfig(tailwindConfig);
</script>

<div id="description">
	<div class="article-section">
		<h1>Transformer とは何か?</h1>

		<p>
			Transformer は、人工知能へのアプローチを根本的に変えてきたニューラル・ネットワーク・アーキテクチャです。Transformer は、2017 年に独創的な論文
			<a
				href="https://dl.acm.org/doi/10.5555/3295222.3295349"
				title="ACM Digital Library"
				target="_blank">"Attention is All You Need"</a
			>
			で初めて紹介され、それ以来、ディープラーニング・モデルの定番アーキテクチャとなり、OpenAI の <strong>GPT</strong>、Meta の <strong>Llama</strong>、Google の <strong>Gemini</strong> などのテキスト生成モデルに採用されています。Transformer は、テキスト以外にも、
			<a
				href="https://huggingface.co/learn/audio-course/en/chapter3/introduction"
				title="Hugging Face"
				target="_blank">オーディオ生成</a
			>,
			<a
				href="https://huggingface.co/learn/computer-vision-course/unit3/vision-transformers/vision-transformers-for-image-classification"
				title="Hugging Face"
				target="_blank">画像認識</a
			>,
			<a href="https://elifesciences.org/articles/82819" title="eLife"
				>タンパク質構造予測</a
			>、さらには
			<a
				href="https://www.deeplearning.ai/the-batch/reinforcement-learning-plus-transformers-equals-efficiency/"
				title="Deep Learning AI"
				target="_blank">ゲームプレイ</a
			>, にも応用されており、さまざまな分野でその汎用性が実証されています。
		</p>
		<p>
			基本的に、テキスト生成 Transformer モデルは、<strong>次の単語の予測</strong>の原理に基づいて動作します。つまり、ユーザーからのテキスト プロンプトが与えられた場合、この入力に続く<em>可能性が最も高い次の単語</em>は何でしょうか。Transformer の核となる革新性とパワーは、自己注意メカニズムの使用にあります。これにより、シーケンス全体を処理し、以前のアーキテクチャよりも効果的に長距離の依存関係をキャプチャできます。
		</p>
		<p>
			GPT-2 ファミリーのモデルは、テキスト生成トランスフォーマーの代表的な例です。Transformer Explainer は、1 億 2,400 万のパラメータを持つ
			<a href="https://huggingface.co/openai-community/gpt2" title="Hugging Face" target="_blank"
				>GPT-2</a
			>
			(小) モデルを採用しています。最新または最も強力なトランスフォーマー モデルではありませんが、現在の最先端のモデルと同じアーキテクチャ コンポーネントと原則を多く共有しているため、基礎を理解するための理想的な出発点となります。
		</p>
	</div>

	<div class="article-section">
		<h1>Transformer アーキテクチャ</h1>

		<p>
			すべてのテキスト生成トランスフォーマーは、次の <strong>3 つの主要コンポーネント</strong> で構成されています:
		</p>
		<ol>
			<li>
				<strong class="bold-purple">埋め込み</strong>: テキスト入力は、単語またはサブワードであるトークンと呼ばれる小さな単位に分割されます。これらのトークンは、単語の意味を捉える埋め込みと呼ばれる数値ベクトルに変換されます。
			</li>
			<li>
				<strong class="bold-purple">Transformer ブロック</strong> は、入力データを処理および変換するモデルの基本的な構成要素です。各ブロックには次のものが含まれます:
				<ul class="">
					<li>
						<strong>アテンション メカニズム</strong>は、Transformer ブロックのコア コンポーネントです。これにより、トークンが他のトークンと通信して、コンテキスト情報や単語間の関係をキャプチャできます。
					</li>
					<li>
						<strong>MLP (多層パーセプトロン) レイヤー</strong>は、各トークンを独立して操作するフィードフォワード ネットワークです。アテンション レイヤーの目的はトークン間で情報をルーティングすることですが、MLP の目的は各トークンの表現を洗練することです。
					</li>
				</ul>
			</li>
			<li>
				<strong class="bold-purple">出力確率</strong>: 最後の線形レイヤーとソフトマックス レイヤーは、処理された埋め込みを確率に変換し、モデルがシーケンス内の次のトークンについて予測できるようにします。
			</li>
		</ol>

		<div class="architecture-section">
			<h2>Embedding</h2>
			<p>
				Transformer モデルを使用してテキストを生成するとします。次のようなプロンプトを追加します: <code>“Data visualization empowers users to”（「データの視覚化により、ユーザーは）	</code> この入力は、モデルが理解して処理できる形式に変換する必要があります。ここで埋め込みが役立ちます。埋め込みは、テキストをモデルが処理できる数値表現に変換します。プロンプトを埋め込みに変換するには、1) 入力をトークン化し、2) トークン埋め込みを取得し、3) 位置情報を追加し、最後に 4) トークンと位置のエンコーディングを追加して、最終的な埋め込みを取得する必要があります。これらの各ステップがどのように実行されるかを見てみましょう。

			</p>
			<div class="figure">
				<img src="./article_assets/embedding.png" width="60%" height="60%" align="middle" />
			</div>
			<div class="figure-caption">
				図 <span class="attention">1</span>. 埋め込みレイヤービューを展開し、入力プロンプトがベクトル表現に変換される様子を示します。プロセスには、<span class="fig-numbering">(1)</span> トークン化、(2) トークン埋め込み、(3) 位置エンコーディング、(4) 最終埋め込みが含まれます。
			</div>
			<div class="article-subsection">
				<h3>Step 1: トークン化</h3>
				<p>
					トークン化とは、入力テキストをトークンと呼ばれるより小さく扱いやすい部分に分割するプロセスです。これらのトークンは、単語またはサブワードです。単語 <code>"Data"</code> と <code>"vizualization"</code> は一意のトークンに対応し、単語 <code>"empowers"</code> は 2つのトークンに分割されます。トークンの完全な語彙は、モデルをトレーニングする前に決定されます。GPT-2 の語彙には、<code>50,257</code> 個の一意のトークンがあります。入力テキストを異なる ID を持つトークンに分割したので、埋め込みからそれらのベクトル表現を取得できます。
				</p>
			</div>
			<div class="article-subsection">
				<h3>Step 2. トークン埋め込み</h3>
				<p>
					GPT-2 Small は、語彙内の各トークンを 768 次元のベクトルとして表します。ベクトルの次元はモデルによって異なります。これらの埋め込みベクトルは、約 3,900 万個のパラメータを含む、形状 <code>(50,257, 768)</code> のマトリックスに格納されます。この大規模なマトリックスにより、モデルは各トークンに意味を割り当てることができます。
				</p>
			</div>
			<div class="article-subsection">
				<h3>Step 3. 位置のエンコーディング</h3>
				<p>
					埋め込みレイヤーは、入力プロンプト内の各トークンの位置に関する情報もエンコードします。モデルによって位置エンコードの方法は異なります。GPT-2 は独自の位置エンコード マトリックスを最初からトレーニングし、それをトレーニング プロセスに直接統合します。
				</p>

				<!-- <div class="article-subsection-l2">
            <h4>Alternative Positional Encoding Approach <strong class='attention'>[POTENTIALLY COLLAPSIBLE]</strong></h4>
            <p>
              Other models, like the original Transformer and BERT,
              use sinusoidal functions for positional encoding.

              This sinusoidal encoding is deterministic and designed to reflect
              the absolute as well as the relative position of each token.
            </p>
            <p>
              Each position in a sequence is assigned a unique mathematical
              representation using a combination of sine and cosine functions.

              For a given position, the sine function represents even dimensions,
              and the cosine function represents odd dimensions within the positional encoding vector.

              This periodic nature ensures that each position has a consistent encoding,
              independent of the surrounding context.
            </p>

            <p>
              Here’s how it works:
            </p>

            <span class='attention'>
              SINUSOIDAL POSITIONAL ENCODING EQUATION
            </span>

            <ul>
              <li>
                <strong>Sine Function</strong>: Used for even indices of the embedding vector.
              </li>
              <li>
                <strong>Cosine Function</strong>: Used for odd indices of the embedding vector.
            </ul>

            <p>
              Hover over individual encoding values in the matrix above to
              see how it's calculated using the sins and cosine functions.
            </p>
          </div> -->
			</div>
			<div class="article-subsection">
				<h3>Step 4. 最後の埋め込み</h3>
				<p>
					最後に、トークンと位置エンコーディングを合計して、最終的な埋め込み表現を取得します。この組み合わせた表現は、トークンの意味と入力シーケンス内の位置の両方をキャプチャします。
				</p>
			</div>
		</div>

		<div class="architecture-section">
			<h2>Transformer ブロック</h2>

			<p>
				Transformer の処理の中核は、マルチヘッド セルフアテンションと多層パーセプトロン層で構成される Transformer ブロックにあります。

				ほとんどのモデルは、複数のこのようなブロックが次々に積み重ねられて構成されています。
				
				トークン表現は、最初のブロックから 12 番目のブロックまで層を経て進化し、モデルが各トークンの複雑な理解を構築できるようにします。
				
				この階層化アプローチにより、入力の高次表現が実現します。
			</p>

			<div class="article-subsection">
				<h3>マルチ・ヘッド Self-Attention</h3>
				<p>
					Self-Attention メカニズムにより、モデルは入力シーケンスの関連部分に焦点を当てることができるため、データ内の複雑な関係や依存関係を捉えることができます。
					この Self-Attention がどのように計算されるかを段階的に見ていきましょう。
				</p>
				<div class="article-subsection-l2">
					<h4>Step 1: クエリ、キー、値のマトリックス</h4>

					<div class="figure">
						<img src="./article_assets/QKV.png" width="80%" align="middle" />
					</div>
					<div class="figure-caption">
						図 <span class="attention">2</span>. 元の埋め込みからクエリ、キー、および値の行列を計算します。
					</div>

					<p>
						各トークンの埋め込みベクトルは、次の 3 つのベクトルに変換されます:
						<span class="q-color">クエリ (Q)</span>、
						<span class="k-color">キー (K)</span>、
						<span class="v-color">値 (V)</span>。これらのベクトルは、入力埋め込み行列に、
						<span class="q-color">Q</span>、
						<span class="k-color">K</span>、
						<span class="v-color">V</span> の学習済み重み行列を掛け合わせることで得られます。これらの行列の背後にある直感を理解するのに役立つ Web 検索の例えを以下に示します:
					</p>
					<ul>
						<li>
							<strong class="q-color font-medium">クエリ (Q)</strong> は、検索エンジン・バーに入力する検索テキストです。これは、<em>「詳細情報を検索」</em> したいトークンです。
						</li>
						<li>
							<strong class="k-color font-medium">キー (K)</strong> は、検索結果ウィンドウ内の各 Web ページのタイトルです。これは、クエリが対象とする可能性のあるトークンを表します。
						</li>
						<li>
							<strong class="v-color font-medium">値 (V)</strong> は、表示される Web ページの実際のコンテンツです。適切な検索語 (クエリ) と関連する結果 (キー) を一致させたら、最も関連性の高いページのコンテンツ (値) を取得します。
						</li>
					</ul>
					<p>
						これらの QKV 値を使用することで、モデルは注目度スコアを計算し、予測を生成する際に各トークンにどの程度の焦点を当てるかを決定します。
					</p>
				</div>
				<div class="article-subsection-l2">
					<h4>Step 2: マスクされた Self-Attention</h4>
					<p>
						マスクされた Self-Attention により、モデルは入力の関連部分に焦点を当てながら、将来のトークンへのアクセスを防ぎ、シーケンスを生成できます。
					</p>

					<div class="figure">
						<img src="./article_assets/attention.png" width="80%" align="middle" />
					</div>
					<div class="figure-caption">
						図 <span class="attention">3</span>. クエリ、キー、および値のマトリックスを使用して、マスクされた Self-Attention を計算します。
					</div>

					<ul>
						<li>
							<strong>Attention スコア</strong>: <span class="q-color">クエリ</span> 行列と <span class="k-color">キー</span> 行列のドット積により、各クエリと各キーの配置が決定され、すべての入力トークン間の関係を反映する正方行列が生成されます。

						</li>
						<li>
							<strong>マスキング</strong>: モデルが将来のトークンにアクセスできないように、Attention マトリックスの上部の三角形にマスクが適用され、これらの値が負の無限大に設定されます。モデルは、将来を「覗き見」することなく、次のトークンを予測する方法を学習する必要があります。
						</li>
						<li>
							<strong>ソフトマックス</strong>: マスキング後、注目スコアは各注目スコアの指数を取るソフトマックス演算によって確率に変換されます。マトリックスの各行の合計は 1 になり、その左側にある他のすべてのトークンの関連性を示します。
						</li>
					</ul>
				</div>
				<div class="article-subsection-l2">
					<h4>Step 3: Output</h4>
					<p>
						モデルはマスクされた Self-Attention スコアを使用し、それを <span class="v-color">Value</span> マトリックスと乗算して、 Self-Attention メカニズムの <span class="purple-color">最終出力</span> を取得します。GPT-2 には <code>12</code> 個の自己注意ヘッドがあり、それぞれがトークン間の異なる関係をキャプチャします。これらのヘッドの出力は連結され、線形投影に渡されます。
					</p>
				</div>

				<div class="article-subsection">
					<h3>MLP: Multi-Layer Perceptron</h3>

					<div class="figure">
						<img src="./article_assets/mlp.png" width="70%" align="middle" />
					</div>
					<div class="figure-caption">
						図 <span class="attention">4</span>. MLP レイヤーを使用して Self-Attention 表現を高次元に投影し、モデルの表現能力を強化します。
					</div>

					<p>
						複数の自己注意ヘッドが入力トークン間の多様な関係を捕捉した後、連結された出力は多層パーセプトロン (MLP) 層に渡され、モデルの表現能力が強化されます。

						MLP ブロックは、間に GELU アクティベーション関数を挟んだ 2 つの線形変換で構成されています。最初の線形変換では、入力の次元が <code>768</code> から <code>3072</code> へと4倍に増加します。
						
						2番目の線形変換では、次元が元のサイズである <code>768</code> に縮小され、後続の層が一貫した次元の入力を受け取ることが保証されます。
						
						Self-Attention メカニズムとは異なり、MLP はトークンを個別に処理し、1つの表現から別の表現に単純にマッピングします。
					</p>
				</div>

				<div class="architecture-section">
					<h2>確率の出力</h2>
					<p>
						入力がすべての Transformer ブロックで処理された後、出力は最後の線形レイヤーに渡され、トークン予測用に準備されます。

						このレイヤーは、最終的な表現を <code>50,257</code> 次元空間に投影します。この空間では、語彙内のすべてのトークンに <code>logit</code> と呼ばれる対応する値があります。
						
						どのトークンも次の単語になる可能性があるため、このプロセスにより、これらのトークンを次の単語になる可能性で簡単にランク付けできます。
						
						次に、softmax 関数を適用して、logit を合計が 1 になる確率分布に変換します。
						
						これにより、次のトークンをその可能性に基づいてサンプリングできます。
					</p>

					<div class="figure">
						<img src="./article_assets/softmax.png" width="60%" align="middle" />
					</div>
					<div class="figure-caption">
						図 <span class="attention">5</span>. 語彙内の各トークンには、モデルの出力ロジットに基づいて確率が割り当てられます。これらの確率により、各トークンがシーケンス内の次の単語になる可能性が決まります。
					</div>

					<p>
						最後のステップは、この分布からサンプリングして次のトークンを生成することです。<code>temperature</code> ハイパーパラメータは、このプロセスで重要な役割を果たします。

						数学的に言えば、これは非常に単純な操作です。モデル出力のロジットを <code>temperature</code> で単純に割るだけです:
					</p>

					<ul>
						<li>
							<code>temperature = 1</code>: ロジットを 1 で割っても、ソフトマックス出力には影響しません。
						</li>
						<li>
							<code>temperature &lt; 1</code>: temperature が低いと、確率分布がシャープになり、モデルの信頼性と決定性が向上し、より予測可能な出力が得られます。
						</li>
						<li>
							<code>temperature &gt; 1</code>: temperature が高いと確率分布が柔らかくなり、生成されるテキストのランダム性が高まります。これは、モデルの<em>創造性</em>と呼ばれることもあります。
						</li>
					</ul>

					<p>
						temperature を調整して、確定的な出力と多様な出力のバランスをとる方法を確認してください。
					</p>
				</div>

				<div class="architecture-section">
					<h2>高度なアーキテクチャ機能</h2>

					<p>
						Transformer モデルのパフォーマンスを向上させる高度なアーキテクチャ機能がいくつかあります。

						モデルの全体的なパフォーマンスには重要ですが、アーキテクチャのコア概念を理解する上ではそれほど重要ではありません。
						
						レイヤー正規化、ドロップアウト、および残差接続は、特にトレーニング フェーズで Transformer モデルの重要なコンポーネントです。
						
						レイヤー正規化はトレーニングを安定させ、モデルの収束を早めます。
						
						ドロップアウトは、ニューロンをランダムに非アクティブ化することでオーバーフィッティングを防止します。
						
						残差接続は、勾配がネットワークを直接流れるようにし、勾配消失の問題を防止するのに役立ちます。
					</p>
					<div class="article-subsection">
						<h3>レイヤー正規化</h3>

						<p>
							レイヤー正規化は、トレーニング プロセスを安定させ、収束を改善するのに役立ちます。

							これは、機能全体の入力を正規化することで機能し、アクティベーションの平均と分散が一貫していることを保証します。
							
							この正規化は、内部共変量シフトに関連する問題を軽減し、モデルがより効果的に学習できるようにし、初期重みに対する感度を低減するのに役立ちます。
							
							レイヤー正規化は、各 Transformer ブロックで 2 回適用されます。1 回は自己注意メカニズムの前、もう 1 回は MLP レイヤーの前です。
						</p>
					</div>
					<div class="article-subsection">
						<h3>ドロップアウト</h3>

						<p>
							ドロップアウトは、トレーニング中にモデルの重みの一部をランダムにゼロに設定することで、ニューラル ネットワークの過剰適合を防ぐために使用される正規化手法です。

							これにより、モデルはより堅牢な機能を学習し、特定のニューロンへの依存度が減り、ネットワークが新しい未知のデータに対してより適切に一般化できるようになります。
							
							モデル推論中は、ドロップアウトは無効になります。
							
							これは基本的に、トレーニング済みのサブネットワークのアンサンブルを使用していることを意味し、モデルのパフォーマンスが向上します。
						</p>
					</div>
					<div class="article-subsection">
						<h3>残差接続</h3>

						<p>
							残差接続は、2015 年に ResNet モデルで初めて導入されました。このアーキテクチャの革新により、非常に深いニューラル ネットワークのトレーニングが可能になり、ディープラーニングに革命が起こりました。

							基本的に、残差接続は 1 つ以上のレイヤーをバイパスし、レイヤーの入力をその出力に追加するショートカットです。
							
							これにより、勾配消失の問題が緩和され、複数の Transformer ブロックが互いに積み重ねられたディープ ネットワークのトレーニングが容易になります。
							
							GPT-2 では、残差接続は各 Transformer ブロック内で 2 回使用されます。1 回は MLP の前、もう 1 回は後です。これにより、勾配がより簡単に流れるようになり、バックプロパゲーション中に前のレイヤーが十分な更新を受け取るようになります。
						</p>
					</div>
				</div>

				<div class="article-section">
					<h1>インタラクティブ機能</h1>
					<p>
						Transformer Explainer はインタラクティブに構築されており、Transformer の内部動作を探索できます。以下は、実際に使用できるインタラクティブ機能の一部です:
					</p>

					<ul>
						<li>
							<strong>独自のテキスト・シーケンスを入力</strong>して、モデルがそれをどのように処理し、次の単語を予測するかを確認します。注意の重み、中間計算を調べ、最終的な出力確率がどのように計算されるかを確認します。

						</li>
						<li>
							<strong>temperature スライダーを使用</strong>して、モデルの予測のランダム性を制御します。
							temperature 値を変更することで、モデル出力をより決定論的に、またはより創造的にする方法を探ります。

						</li>
						<li>
							<strong>アテンション・マップを操作</strong>して、モデルが入力シーケンス内のさまざまなトークンにどのように焦点を当てているかを確認します。トークンの上にマウスを移動してアテンションの重みを強調表示し、モデルが単語間のコンテキストと関係をどのようにキャプチャするかを調べます。
						</li>
					</ul>
				</div>

				<div class="article-section">
					<h2>ビデオ・チュートリアル</h2>
					<div class="video-container">
						<iframe
							src="https://www.youtube.com/embed/ECR4oAwocjs"
							frameborder="0"
							allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
							allowfullscreen
						>
						</iframe>
					</div>
				</div>

				<div class="article-section">
					<h2>Transformer Explainer はどのように実装されますか?</h2>
					<p>
						Transformer Explainer には、ブラウザで直接実行されるライブ GPT-2 (small) モデルが搭載されています。

						このモデルは、Andrej Karpathy の <a href="https://github.com/karpathy/nanoGPT" title="Github" target="_blank">nanoGPT プロジェクト</a> による GPT の PyTorch 実装から派生したもので、ブラウザ内でシームレスに実行できるように <a href="https://onnxruntime.ai/" title="ONNX" target="_blank">ONNX Runtime</a> に変換されています。

						インターフェイスは JavaScript を使用して構築されており、フロントエンド フレームワークとして <a href="https://kit.svelte.dev/" title="Svelte" target="_blank">Svelte</a> が使用され、動的な視覚化を作成するために <a href="http://D3.js" title="D3" target="_blank">D3.js</a> が使用されています。数値は、ユーザー入力に従ってライブで更新されます。
					</p>
				</div>

				<div class="article-section">
					<h2>Transformer Explainer を開発したのは誰ですか?</h2>
					<p>
						Transformer Explainer は以下のメンバーで開発されました。

						<a href="https://aereeeee.github.io/" target="_blank">Aeree Cho</a>,
						<a href="https://www.linkedin.com/in/chaeyeonggracekim/" target="_blank">Grace C. Kim</a
						>,
						<a href="https://alexkarpekov.com/" target="_blank">Alexander Karpekov</a>,
						<a href="https://alechelbling.com/" target="_blank">Alec Helbling</a>,
						<a href="https://zijie.wang/" target="_blank">Jay Wang</a>,
						<a href="https://seongmin.xyz/" target="_blank">Seongmin Lee</a>,
						<a href="https://bhoov.com/" target="_blank">Benjamin Hoover</a>, and
						<a href="https://poloclub.github.io/polochau/" target="_blank">Polo Chau</a>

						at the Georgia Institute of Technology.
					</p>
				</div>
			</div>
		</div>
	</div>
</div>

<style lang="scss">
	a {
		color: theme('colors.blue.500');

		&:hover {
			color: theme('colors.blue.700');
		}
	}

	.bold-purple {
		color: theme('colors.purple.700');
		font-weight: bold;
	}

	code {
		color: theme('colors.gray.500');
		background-color: theme('colors.gray.50');
		font-family: theme('fontFamily.mono');
	}

	.q-color {
		color: theme('colors.blue.400');
	}

	.k-color {
		color: theme('colors.red.400');
	}

	.v-color {
		color: theme('colors.green.400');
	}

	.purple-color {
		color: theme('colors.purple.500');
	}

	.article-section {
		padding-bottom: 2rem;
	}
	.architecture-section {
		padding-top: 1rem;
	}
	.video-container {
		position: relative;
		padding-bottom: 56.25%; /* 16:9 aspect ratio */
		height: 0;
		overflow: hidden;
		max-width: 100%;
		background: #000;
	}

	.video-container iframe {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
	}

	#description {
		padding-bottom: 3rem;
		margin-left: auto;
		margin-right: auto;
		max-width: 78ch;
	}

	#description h1 {
		color: theme('colors.purple.700');
		font-size: 2.2rem;
		font-weight: 300;
		padding-top: 1rem;
	}

	#description h2 {
		// color: #444;
		color: theme('colors.purple.700');
		font-size: 2rem;
		font-weight: 300;
		padding-top: 1rem;
	}

	#description h3 {
		color: theme('colors.gray.700');
		font-size: 1.6rem;
		font-weight: 200;
		padding-top: 1rem;
	}

	#description h4 {
		color: theme('colors.gray.700');
		font-size: 1.6rem;
		font-weight: 200;
		padding-top: 1rem;
	}

	#description p {
		margin: 1rem 0;
	}

	#description p img {
		vertical-align: middle;
	}

	#description .figure-caption {
		font-size: 0.8rem;
		margin-top: 0.5rem;
		text-align: center;
		margin-bottom: 2rem;
	}

	#description ol {
		margin-left: 3rem;
		list-style-type: decimal;
	}

	#description li {
		margin: 0.6rem 0;
	}

	#description p,
	#description div,
	#description li {
		color: theme('colors.gray.600');
		// font-size: 17px;
		// font-size: 15px;
		line-height: 1.6;
	}

	#description small {
		font-size: 0.8rem;
	}

	#description ol li img {
		vertical-align: middle;
	}

	#description .video-link {
		color: theme('colors.blue.600');
		cursor: pointer;
		font-weight: normal;
		text-decoration: none;
	}

	#description ul {
		list-style-type: disc;
		margin-left: 2.5rem;
		margin-bottom: 1rem;
	}

	#description a:hover,
	#description .video-link:hover {
		text-decoration: underline;
	}

	.figure,
	.video {
		width: 100%;
		display: flex;
		flex-direction: column;
		align-items: center;
	}
</style>
