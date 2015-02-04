<div id="title">
  <h1>
    {% unless site.git == "" %}
		  <a id="git-ic" href="{{site.git}}"><i class="fa fa-github"></i></a>	
    {% endunless %}

    {% unless site.stackoverflow == "" %}
      <a id="so-ic" href="{{site.stackoverflow}}"><i class="fa fa-stack-overflow"></i></a>
    {% endunless %}
		
    <a id="sitename" href="/">alfredoxperez.github.io</a>
  </h1>

  <!-- Add something about you in p tag-->
  <p>Welcome, I'm a software engineer for an ad tech company in Boston, MA USA</p>
  <p class="contact-text">contact me <a href="mailto:alfredoxperez@gmail.com">alfredoxperez (at) gmail.com</a></p>
  <hr/>

  
  {% include nav-links.md %} 
	 
</div>


