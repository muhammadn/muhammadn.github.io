---
layout: default
comments: true
---

{% for post in site.posts %}
  <div id="{{ post.date | date: "%F-%H" }}" class="unit whole align-left post">
        <article itemscope itemtype="http://schema.org/BlogPosting">
                <h2>{{ post.title }}<br>
                <small class="chip">{{ post.date | date: "%b %-d, %Y" }}</small>
                </h2>

                <div class="post-content">
                        {{ post.content }}
                </div>

                {% if post.author %}
                <span class="chip" itemprop="author" itemscope itemtype="http://schema.org/Person">
                        <span itemprop="name">Article by {{ post.author }}</span>
                </span>
                {% endif %}

        </article>
  </div>
  {% if page.comments %}
    <div id="disqus_thread"></div>
    <script>

        /**
        *  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
        *  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables*/
        var disqus_config = function () {
          this.page.url = "http://unrealasia.net{{ page.url }}";  // Replace PAGE_URL with your page's canonical URL variable
          this.page.identifier = "{{ page.id }}"; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
        };

        (function() { // DON'T EDIT BELOW THIS LINE
            var d = document, s = d.createElement('script');
             s.src = 'https://unrealasia.disqus.com/embed.js';
            s.setAttribute('data-timestamp', +new Date());
           (d.head || d.body).appendChild(s);
        })();
    </script>
    <noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
                                
  {% endif %}
{% endfor %}
