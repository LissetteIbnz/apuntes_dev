---


---

<h1 id="webpack-config-prod">WEBPACK CONFIG PROD</h1>
<blockquote>
<p>Desde 0 a configuración de modo producción</p>
</blockquote>
<h2 id="tabla-de-contenido">TABLA DE CONTENIDO</h2>
<ol>
<li>webpack 4: comenzar con cero config</li>
<li>webpack 4: modo de producción y desarrollo</li>
<li>webpack 4: anulando la entrada/salida predeterminada</li>
<li>webpack 4: transpilar Javascript ES6 con Babel</li>
<li>webpack 4: configurar con React</li>
<li>webpack 4: plugin de webpack HTML</li>
<li>webpack 4: extracción de CSS a un archivo</li>
<li>webpack 4: servidor de desarrollo webpack</li>
<li>webpack 4: recursos</li>
</ol>
<h3 id="webpack-4-comenzar-con-cero-config">webpack 4: comenzar con cero config</h3>
<blockquote>
<p>webpack 4 no necesita un archivo de configuración por defecto.</p>
</blockquote>
<p>Crea un nuevo directorio y muévete hacia él:</p>
<pre><code>mkdir webpack-config-prod &amp;&amp; cd $ _
</code></pre>
<p>Inicializa un paquete.json ejecutando:</p>
<pre><code>npm init -y
</code></pre>
<p>y tira del webpack4 en:</p>
<pre><code>npm install webpack --save-dev
</code></pre>
<p>o lo que es lo mismo <code>npm i webpack -D</code></p>
<p>Necesitamos <code>webpack-cli</code> también, que vive como un paquete separado:</p>
<pre><code>npm i webpack-cli -D
</code></pre>
<p>Ahora abra <code>package.json</code> y agregue un script de compilación:</p>
<pre class=" language-json"><code class="prism  language-json"><span class="token string">"scripts"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
  <span class="token string">"build"</span><span class="token punctuation">:</span> <span class="token string">"webpack"</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Cierra el archivo y guárdalo.</p>
<p>Intenta ejecutar:</p>
<pre><code>npm run build
</code></pre>
<p>y mira lo que pasa:</p>
<pre class=" language-bash"><code class="prism  language-bash">ERROR <span class="token keyword">in</span> Entry module not found: Error: Can<span class="token string">'t resolve '</span>./src<span class="token string">' in '</span>~/webpack-4-quickstart'
</code></pre>
<p>Webpack4 está buscando un punto de entrada <code>./src</code><br>
El <strong>entry point</strong> es el archivo que busca webpack para comenzar a construir su paquete de Javascript.</p>
<p>En la versión anterior de webpack, el punto de entrada debía definirse dentro de un archivo de configuración denominado <code>webpack.config.js</code>. Pero a partir del <strong>webpack 4 no es necesario definir el punto de entrada</strong> : ¡cogerá <strong>./src/index.js</strong> como valor predeterminado!</p>
<p>Sigamos… ahora creamos el archivo <code>./src/index.js</code> con el siguiente contenido:</p>
<pre><code>console.log(`I'm a silly entry point`);
</code></pre>
<p>Y ejecutar <code>npm run build</code>de nuevo.<br>
Recibiremos el bundle en la ruta <code>~/webpack-config-prod/dist/main.js</code><br>
Observa que no es necesario indicarle un destino de salida. Por defecto utilizará <code>dist/main.js</code>. Por tanto, el primer cambio más importante es que <strong>webpack4 no necesita archivo de configuración</strong>.</p>
<p>Se verá en <strong>./src/index.js</strong> como el punto de entrada predeterminado. Además, sacará el bundle en <strong>./dist/main.js</strong> .</p>
<h2 id="webpack-4-modo-producción-y-desarrollo">webpack 4: modo producción y desarrollo</h2>
<p>Tener 2 archivos de configuración es un patrón común en webpack.</p>
<p>Un proyecto típico puede tener:</p>
<ul>
<li>un <strong>archivo de configuración para desarrollo</strong> , para definir el webpack-dev-server y otras cosas</li>
<li>un <strong>archivo de configuración para producción</strong> , para definir <strong>UglifyJSPlugin</strong> , sourcemaps, etc.</li>
</ul>
<p>Los modos de trabajo se configuran en el archivo de <code>package.json</code> de la siguiente manera:</p>
<pre class=" language-json"><code class="prism  language-json"><span class="token string">"scripts"</span> <span class="token punctuation">:</span> <span class="token punctuation">{</span>
  <span class="token string">"dev"</span> <span class="token punctuation">:</span> <span class="token string">"webpack --mode development"</span> <span class="token punctuation">,</span>
  <span class="token string">"build"</span> <span class="token punctuation">:</span> <span class="token string">"webpack --mode production"</span>
<span class="token punctuation">}</span>
</code></pre>
<blockquote>
<p>Se puede abreviar como -d para --mode development y -p para --mode production.</p>
</blockquote>
<p>Ahora intenta ejecutar:</p>
<pre><code>npm run dev
</code></pre>
<p>y echa un vistazo a <strong>./dist/main.js</strong>. El código no está optimizado.</p>
<p>Ahora intenta ejecutar:</p>
<pre><code>npm run build
</code></pre>
<p>y echa un vistazo a <strong>./dist/main.js</strong> para ver un <strong>paquete reducido</strong></p>
<p><strong>El modo de producción</strong> permite todo tipo de optimizaciones desde el primer momento. Incluye minificación, scope hoisting (elevación de alcance), tree-shaking y más…</p>
<p>El modo de desarrollo, por otro lado, está optimizado para la velocidad y no hace más que proporcionar un paquete no minificado.</p>
<h2 id="webpack-4-anulando-la-entrada--salida-predeterminada">webpack 4: anulando la entrada / salida predeterminada</h2>
<p>Los puntos de entrada/salida se configuran en el archivo <code>package.json</code></p>
<p>Aquí hay un ejemplo:</p>
<pre class=" language-json"><code class="prism  language-json"><span class="token string">"scripts"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
  <span class="token string">"dev"</span><span class="token punctuation">:</span> <span class="token string">"webpack --mode development ./foo/src/js/index.js --output ./foo/main.js"</span> <span class="token punctuation">,</span>
  <span class="token string">"build"</span><span class="token punctuation">:</span> <span class="token string">"webpack --mode production ./foo/src/js/index.js --output ./foo/main.js"</span>
<span class="token punctuation">}</span>
</code></pre>
<h2 id="webpack-4-transpiling-javascript-es6-con-babel">webpack 4: transpiling Javascript ES6 con Babel</h2>
<p>Como no todos los navegadores saben cómo lidiar con ES6 necesitamos algún tipo de transformación.</p>
<p>Este paso de transformación se llama <strong>transpiling</strong>. Transpiling es el acto de tomar ES6 y hacerlo comprensible para los navegadores más antiguos.</p>
<p>Webpack no sabe cómo hacer la transformación, pero tiene <strong>loaders (cargadores)</strong>: piensa en ellos como transformadores.</p>
<p><strong>babel-loader</strong> es el cargador de webpack para transpilar ES6 y superior, hasta ES5.</p>
<p>Para comenzar a usar el loader, necesitamos instalar un conjunto de dependencias. En particular:</p>
<ul>
<li>babel-core: El core que necesitamos para cargar presets</li>
<li>babel-loader: Es un loader para poder utilizar babel con webpack</li>
<li>babel-preset-env: Para transpilar características de ES6 en adelante</li>
</ul>
<p>Vamos a hacerlo:</p>
<pre><code>npm i babel-core babel-loader babel-preset-env -D
</code></pre>
<p>Luego configura Babel creando un nuevo archivo llamado <strong>.babelrc</strong> dentro de la carpeta del proyecto:</p>
<pre class=" language-js"><code class="prism  language-js"><span class="token punctuation">{</span>
  <span class="token string">"presets"</span><span class="token punctuation">:</span> <span class="token punctuation">[</span>
    <span class="token string">"env"</span>
  <span class="token punctuation">]</span>
<span class="token punctuation">}</span>
</code></pre>
<p>En este punto tenemos 2 opciones para configurar babel-loader:</p>
<ul>
<li>usando un archivo de configuración para el webpack</li>
<li>usando <code>--module-bind</code> en tus scripts npm</li>
</ul>
<p>El concepto de <strong>configuración cero en webpack 4</strong> se aplica a:</p>
<ul>
<li>el <strong>punto de entrada</strong>. Predeterminado a ./src/index.js</li>
<li>la <strong>salida</strong>. Predeterminado en ./dist/main.js</li>
<li><strong>modo de producción y desarrollo</strong> (no es necesario crear 2 confs separados para producción y desarrollo)</li>
</ul>
<p>Y es suficiente, pero para usar los loaders en el webpack 4, aún debes crear un archivo de configuración.</p>
<h3 id="webpack-4-uso-de-babel-loader-con-un-archivo-de-configuración">webpack 4: uso de babel-loader con un archivo de configuración</h3>
<p>Proporciona a webpack un archivo de configuración para usar babel-loader de la manera más clásica.</p>
<p>Cree un nuevo archivo llamado <code>webpack.config.js</code> y configura el loader:</p>
<pre class=" language-js"><code class="prism  language-js">module<span class="token punctuation">.</span>exports  <span class="token operator">=</span>  <span class="token punctuation">{</span>
  module<span class="token punctuation">:</span>  <span class="token punctuation">{</span>
    rules<span class="token punctuation">:</span>  <span class="token punctuation">[</span>
      <span class="token punctuation">{</span>
        test<span class="token punctuation">:</span>  <span class="token regex">/\.js$/</span><span class="token punctuation">,</span>
        exclude<span class="token punctuation">:</span>  <span class="token regex">/node_modules/</span><span class="token punctuation">,</span>
        use<span class="token punctuation">:</span>  <span class="token punctuation">{</span>
          loader<span class="token punctuation">:</span>  <span class="token string">"babel-loader"</span>
        <span class="token punctuation">}</span>
      <span class="token punctuation">}</span>
    <span class="token punctuation">]</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>
</code></pre>
<p>No es necesario especificar el punto de entrada a menos que desees personalizarlo.</p>
<p>A continuación, abra <strong>./src/index.js</strong> y escriba algo de ES6:</p>
<pre class=" language-js"><code class="prism  language-js"><span class="token keyword">const</span> arr <span class="token operator">=</span> <span class="token punctuation">[</span><span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">]</span><span class="token punctuation">;</span>
<span class="token keyword">const</span> <span class="token function-variable function">iAmJavascriptES6</span> <span class="token operator">=</span> <span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">=&gt;</span> console<span class="token punctuation">.</span><span class="token function">log</span><span class="token punctuation">(</span><span class="token operator">...</span>arr<span class="token punctuation">)</span><span class="token punctuation">;</span>
window<span class="token punctuation">.</span>iAmJavascriptES6 <span class="token operator">=</span> iAmJavascriptES6<span class="token punctuation">;</span>
</code></pre>
<p>Finalmente crea el paquete con:</p>
<pre><code>npm run build
</code></pre>
<p>Ahora echa un vistazo a <strong>./dist/main.js</strong> para ver el código transpilado.</p>
<h3 id="webpack-4-uso-de-babel-loader-sin-un-archivo-de-configuración">webpack 4: uso de babel-loader sin un archivo de configuración</h3>
<p>Hay otro método para usar cargadores webpack.</p>
<p>El indicador de <code>--module-bind</code> te permite especificar cargadores desde la línea de comando.</p>
<p>Para usar babel-loader sin un archivo de configuración, configura tus scripts npm en <strong>package.json</strong> así:</p>
<pre class=" language-json"><code class="prism  language-json"><span class="token string">"scripts"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
    <span class="token string">"dev"</span><span class="token punctuation">:</span> <span class="token string">"webpack --mode development --module-bind js=babel-loader"</span><span class="token punctuation">,</span>
    <span class="token string">"build"</span><span class="token punctuation">:</span> <span class="token string">"webpack --mode production --module-bind js=babel-loader"</span>
  <span class="token punctuation">}</span>
</code></pre>
<p>Y estás listo para ejecutar la compilación.</p>
<h2 id="webpack-4-configuración-con-react">webpack 4: configuración con React</h2>
<blockquote>
<p>Primero será necesario instalar y configurar Babel.</p>
</blockquote>
<p>Instalar React con:</p>
<pre><code>npm i react react-dom -D
</code></pre>
<p>A continuación, agrega babel-preset-react (para transpilar JSX):</p>
<pre><code>npm i babel-preset-react -D
</code></pre>
<p>Configure el preset en <strong>.babelrc</strong> :</p>
<pre class=" language-js"><code class="prism  language-js"><span class="token punctuation">{</span>
	<span class="token string">"presets"</span><span class="token punctuation">:</span> <span class="token punctuation">[</span>
		<span class="token string">"env"</span><span class="token punctuation">,</span>
		<span class="token string">"react"</span>
	<span class="token punctuation">]</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Para poner a prueba las cosas, puedes crear un componente ficticio React en <code>./src/app.js</code> :</p>
<pre class=" language-js"><code class="prism  language-js"><span class="token keyword">import</span> React <span class="token keyword">from</span> <span class="token string">"react"</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> ReactDOM <span class="token keyword">from</span> <span class="token string">"react-dom"</span><span class="token punctuation">;</span>

<span class="token keyword">const</span> <span class="token function-variable function">App</span> <span class="token operator">=</span> <span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">=&gt;</span> <span class="token punctuation">{</span>
  <span class="token keyword">return</span> <span class="token punctuation">(</span>
    <span class="token operator">&lt;</span>div<span class="token operator">&gt;</span>
      <span class="token operator">&lt;</span>p<span class="token operator">&gt;</span>React here<span class="token operator">!</span><span class="token operator">&lt;</span><span class="token operator">/</span>p<span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span><span class="token operator">/</span>div<span class="token operator">&gt;</span>
  <span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>

<span class="token keyword">export</span> <span class="token keyword">default</span> App<span class="token punctuation">;</span>

ReactDOM<span class="token punctuation">.</span><span class="token function">render</span><span class="token punctuation">(</span><span class="token operator">&lt;</span>App<span class="token operator">/</span><span class="token operator">&gt;</span><span class="token punctuation">,</span> document<span class="token punctuation">.</span><span class="token function">getElementById</span><span class="token punctuation">(</span><span class="token string">"app"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p>A continuación, importa el componente en <code>./src/index.js</code>:</p>
<pre><code>import App from "./app";
</code></pre>
<p>y ejecuta <code>npm run build</code> de nuevo.</p>
<h2 id="webpack-4-el-html-webpack-plugin">webpack 4: el HTML webpack plugin</h2>
<p>Webpack necesita dos componentes adicionales para procesar HTML: html-webpack-plugin y html-loader.</p>
<p>Agregue las dependencias con:</p>
<pre><code>npm i html-webpack-plugin html-loader -D
</code></pre>
<p>Luego actualiza la configuración de webpack:</p>
<pre class=" language-js"><code class="prism  language-js"><span class="token keyword">const</span> HtmlWebPackPlugin <span class="token operator">=</span> <span class="token function">require</span><span class="token punctuation">(</span><span class="token string">"html-webpack-plugin"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

module<span class="token punctuation">.</span>exports <span class="token operator">=</span> <span class="token punctuation">{</span>
  module<span class="token punctuation">:</span> <span class="token punctuation">{</span>
    rules<span class="token punctuation">:</span> <span class="token punctuation">[</span>
      <span class="token punctuation">{</span>
        test<span class="token punctuation">:</span> <span class="token regex">/\.js$/</span><span class="token punctuation">,</span>
        exclude<span class="token punctuation">:</span> <span class="token regex">/node_modules/</span><span class="token punctuation">,</span>
        use<span class="token punctuation">:</span> <span class="token punctuation">{</span>
          loader<span class="token punctuation">:</span> <span class="token string">"babel-loader"</span>
        <span class="token punctuation">}</span>
      <span class="token punctuation">}</span><span class="token punctuation">,</span>
      <span class="token punctuation">{</span>
        test<span class="token punctuation">:</span> <span class="token regex">/\.html$/</span><span class="token punctuation">,</span>
        use<span class="token punctuation">:</span> <span class="token punctuation">[</span>
          <span class="token punctuation">{</span>
            loader<span class="token punctuation">:</span> <span class="token string">"html-loader"</span><span class="token punctuation">,</span>
            options<span class="token punctuation">:</span> <span class="token punctuation">{</span> minimize<span class="token punctuation">:</span> <span class="token boolean">true</span> <span class="token punctuation">}</span>
          <span class="token punctuation">}</span>
        <span class="token punctuation">]</span>
      <span class="token punctuation">}</span>
    <span class="token punctuation">]</span>
  <span class="token punctuation">}</span><span class="token punctuation">,</span>
  plugins<span class="token punctuation">:</span> <span class="token punctuation">[</span>
    <span class="token keyword">new</span> <span class="token class-name">HtmlWebPackPlugin</span><span class="token punctuation">(</span><span class="token punctuation">{</span>
      template<span class="token punctuation">:</span> <span class="token string">"./src/index.html"</span><span class="token punctuation">,</span>
      filename<span class="token punctuation">:</span> <span class="token string">"./index.html"</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span>
  <span class="token punctuation">]</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>
</code></pre>
<p>A continuación, crea un archivo HTML en <code>./src/index.html</code>:</p>
<pre class=" language-html"><code class="prism  language-html"><span class="token doctype">&lt;!DOCTYPE html&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>html</span> <span class="token attr-name">lang</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>en<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>head</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>meta</span> <span class="token attr-name">charset</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>utf-8<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>title</span><span class="token punctuation">&gt;</span></span>webpack 4 quickstart<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>title</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>head</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>body</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>div</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>app<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>div</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>body</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>html</span><span class="token punctuation">&gt;</span></span>
</code></pre>
<p>ejecutar la compilación con:</p>
<pre><code>npm run build
</code></pre>
<p>y echa un vistazo a la carpeta <code>./dist</code>. Deberías ver el HTML resultante.</p>
<blockquote>
<p>No es necesario incluir su Javascript dentro del archivo HTML: el bundle se inyectará automáticamente.</p>
</blockquote>
<p>Abre <code>./dist/index.html</code> en tu navegador: ¡deberías ver el componente React funcionando!</p>
<p>Como puede ver, nada ha cambiado en lo que respecta al manejo de HTML. Webpack 4 sigue siendo un paquete de módulos con el objetivo de Javascript.</p>
<p>Pero hay planes para agregar HTML como módulo (HTML como punto de entrada).</p>
<h2 id="webpack-4-extracción-de-css-a-un-archivo">webpack 4: extracción de CSS a un archivo</h2>
<blockquote>
<p>En el pasado, era un trabajo para <strong>extract-text-webpack-plugin</strong> .<br>
Lamentablemente, dicho complemento no funciona bien con webpack 4.</p>
</blockquote>
<p><em>NOTA: asegúrate de actualizar webpack a la versión 4.2.0. ¡De lo contrario, mini-css-extract-plugin no funcionará!</em></p>
<p>Instala el complemento y el loader css con:</p>
<pre><code>npm i mini-css-extract-plugin css-loader -D
</code></pre>
<p>A continuación, crea un archivo CSS para probar cosas:</p>
<pre class=" language-css"><code class="prism  language-css"><span class="token comment">/**/</span>
<span class="token comment">/* CREATE THIS FILE IN ./src/main.css */</span>
<span class="token comment">/**/</span>

<span class="token selector">body </span><span class="token punctuation">{</span>
    <span class="token property">line-height</span><span class="token punctuation">:</span> <span class="token number">2</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Configure tanto el complemento como el loader:</p>
<pre class=" language-js"><code class="prism  language-js"><span class="token keyword">const</span> HtmlWebPackPlugin <span class="token operator">=</span> <span class="token function">require</span><span class="token punctuation">(</span><span class="token string">"html-webpack-plugin"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token keyword">const</span> MiniCssExtractPlugin <span class="token operator">=</span> <span class="token function">require</span><span class="token punctuation">(</span><span class="token string">"mini-css-extract-plugin"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

module<span class="token punctuation">.</span>exports <span class="token operator">=</span> <span class="token punctuation">{</span>
  module<span class="token punctuation">:</span> <span class="token punctuation">{</span>
    rules<span class="token punctuation">:</span> <span class="token punctuation">[</span>
      <span class="token punctuation">{</span>
        test<span class="token punctuation">:</span> <span class="token regex">/\.js$/</span><span class="token punctuation">,</span>
        exclude<span class="token punctuation">:</span> <span class="token regex">/node_modules/</span><span class="token punctuation">,</span>
        use<span class="token punctuation">:</span> <span class="token punctuation">{</span>
          loader<span class="token punctuation">:</span> <span class="token string">"babel-loader"</span>
        <span class="token punctuation">}</span>
      <span class="token punctuation">}</span><span class="token punctuation">,</span>
      <span class="token punctuation">{</span>
        test<span class="token punctuation">:</span> <span class="token regex">/\.html$/</span><span class="token punctuation">,</span>
        use<span class="token punctuation">:</span> <span class="token punctuation">[</span>
          <span class="token punctuation">{</span>
            loader<span class="token punctuation">:</span> <span class="token string">"html-loader"</span><span class="token punctuation">,</span>
            options<span class="token punctuation">:</span> <span class="token punctuation">{</span> minimize<span class="token punctuation">:</span> <span class="token boolean">true</span> <span class="token punctuation">}</span>
          <span class="token punctuation">}</span>
        \<span class="token punctuation">]</span>
      <span class="token punctuation">}</span><span class="token punctuation">,</span>
      <span class="token punctuation">{</span>
        test<span class="token punctuation">:</span> <span class="token regex">/\.css$/</span><span class="token punctuation">,</span>
        use<span class="token punctuation">:</span> <span class="token punctuation">[</span>MiniCssExtractPlugin<span class="token punctuation">.</span>loader<span class="token punctuation">,</span> <span class="token string">"css-loader"</span><span class="token punctuation">]</span>
      <span class="token punctuation">}</span>
    \<span class="token punctuation">]</span>
  <span class="token punctuation">}</span><span class="token punctuation">,</span>
  plugins<span class="token punctuation">:</span> <span class="token punctuation">[</span>
    <span class="token keyword">new</span> <span class="token class-name">HtmlWebPackPlugin</span><span class="token punctuation">(</span><span class="token punctuation">{</span>
      template<span class="token punctuation">:</span> <span class="token string">"./src/index.html"</span><span class="token punctuation">,</span>
      filename<span class="token punctuation">:</span> <span class="token string">"./index.html"</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">,</span>
    <span class="token keyword">new</span> <span class="token class-name">MiniCssExtractPlugin</span><span class="token punctuation">(</span><span class="token punctuation">{</span>
      filename<span class="token punctuation">:</span> <span class="token string">"[name].css"</span><span class="token punctuation">,</span>
      chunkFilename<span class="token punctuation">:</span> <span class="token string">"[id].css"</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span>
  <span class="token punctuation">]</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>
</code></pre>
<p>Finalmente importe el CSS en el punto de entrada:</p>
<pre class=" language-js"><code class="prism  language-js"><span class="token comment">//</span>
<span class="token comment">// PATH OF THIS FILE: ./src/index.js</span>
<span class="token comment">//</span>

<span class="token keyword">import</span> style <span class="token keyword">from</span> <span class="token string">"./main.css"</span><span class="token punctuation">;</span>
</code></pre>
<p>ejecutar la construcción:</p>
<pre><code>npm run build
</code></pre>
<p>y echa un vistazo en la carpeta <strong>./dist</strong> . ¡Deberías ver el CSS resultante!</p>
<blockquote>
<p><strong>extract-text-webpack-plugin</strong> no funciona con webpack 4.<br>
Usa <strong>mini-css-extract-plugin</strong> en su lugar.</p>
</blockquote>
<h2 id="webpack-4-el-servidor-de-desarrollo-webpack-dev-server">webpack 4: el servidor de desarrollo webpack-dev-server</h2>
<p>Para no tener que ejecutar <code>npm run dev</code> cada vez que realizas cambios en el código, configuremos el servidor de desarrollo <code>webpack-dev-server</code></p>
<p>Una vez configurado, automáticamente actualizará la ventana del navegador cada vez que cambie un archivo.</p>
<p>Para configurar el servidor de desarrollo de webpack, instala el paquete con:</p>
<pre><code>npm i webpack-dev-server -D
</code></pre>
<p>A continuación, abre <code>package.json</code> y ajuste los scripts de la siguiente manera:</p>
<pre class=" language-json"><code class="prism  language-json"><span class="token string">"scripts"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
  <span class="token string">"start"</span><span class="token punctuation">:</span> <span class="token string">"webpack-dev-server --mode development --open"</span><span class="token punctuation">,</span>
  <span class="token string">"build"</span><span class="token punctuation">:</span> <span class="token string">"webpack --mode production"</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Ahora, ejecuta:</p>
<pre><code>npm start
</code></pre>
<p>Verás que webpack-dev-server está lanzando su aplicación dentro del navegador.</p>
<h2 id="webpack-4-recursos">webpack 4: recursos</h2>
<ul>
<li>Artículo traducido y adaptado de <a href="https://www.valentinog.com/blog/webpack-4-tutorial/#more-901">Valentino Gagliardi</a></li>
<li>Repo Github para el tutorial =&gt; <a href="https://github.com/valentinogagliardi/webpack-4-quickstart">webpack-4-quickstart</a></li>
<li>Lista de recursos geniales sobre webpack 4 =&gt; <a href="https://github.com/valentinogagliardi/awesome-webpack-4">awesome-webpack-4</a></li>
<li>Sería negligente por no mencionar <a href="https://survivejs.com/webpack/">SurviveJS webpack 4</a> por Juho Vepsäläinen</li>
</ul>

