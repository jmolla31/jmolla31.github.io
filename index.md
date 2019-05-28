# 'sup bro?

Before we begin, if you see typos or phrases that don't make much sense please take into consideration that I'm not a native english speaker ;) 

My paypal is open if you want to pay me some classes (or a beer or two).

## Post List

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>

