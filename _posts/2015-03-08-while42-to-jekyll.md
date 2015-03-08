---
layout: post
title:  "Moving While42 to Jekyll"
date:   2015-03-08 18:34:59 +0800
tags: jekyll while42
---
2 months ago we opened a [while42][w42] chapter in Taipei. We had a [first meeting][meeting] in Taipei Harckerspace, sponsored by [Gandi][gandi].

When Sylvain, on the organizers mailing-list, launched a call to help renew the [website while42.org][w42], I proposed to switch it from php/mysql pages to a much simpler [github pages][gh-pages] setup, based on [jekyll][jekyll]. Everybody was happy with the idea so I went for it.

Easy setup
==============

Jekyll was [created in 2009][jekyll-contribs] by [Tom Preston-Werner][mojombo], the founder of github, as a side project. Since then it has been integrated as a way to publish pre-generated static pages on github, mostly to present projects. But there are many people just using it as a hosting platform for their blogs. 

The idea is to have some code generating static web pages out of templates and data files. With years, Jekyll added support for SASS and coffeescript preprocessing. And recently, an arbitrary data system was added. It can replace a small database by yaml files.

Pages generation
==================

Jekyll is written in ruby, but for publishing pages on github, you don't really need to have ruby installed. The processing is triggered for each `git push`. But it still can be convenient to have ruby installed for rendering pages locally before pushing. If there is a syntax error in a template, github will send you a mail with details and will refuse to generate a new version. So it's quite robust and fail safe.

The side effect of having pages pre-generated is that they are of course much faster to serve, but also there is no question about the security, the portability and things are all along much simpler to manage from an operational point of view.

Note that for local generation I also created a simple [Dockerfile][dockerfile], so that people that don't want to install ruby can still pre-generate the pages locally before push.

Template system
=================

The template system used in Jekyll is named [Liquid][liquid], it was developed by spotify for their own system and published as open source. 

Templating is great, some common parts of pages can be extracted and replaced by includes, like in the case of our While42 photos at the bottom:

This is in `_includes/home/photos.md`:

    [![](http://farm4.staticflickr.com/3806/9372559708_41ca1ede4b_m.jpg)](http://www.flickr.com/photos/while42/9372559708)
    [![](http://farm4.staticflickr.com/3711/9269942997_809ff5a11a_m.jpg)](http://www.flickr.com/photos/while42/9269942997)  
    [![](http://farm4.staticflickr.com/3773/9215411783_e337f85080_m.jpg)](http://www.flickr.com/photos/while42/9215411783)
    [![](http://farm8.staticflickr.com/7386/9218184500_2e8e538342_m.jpg)](http://www.flickr.com/photos/while42/9218184500)  
    [![](http://farm6.staticflickr.com/5339/9158820739_837dd64533_m.jpg)](http://www.flickr.com/photos/while42/9158820739)
    [![](http://farm8.staticflickr.com/7415/9050451575_4ae0ff0113_m.jpg)](http://www.flickr.com/photos/while42/9050451575)  
    [![](http://farm8.staticflickr.com/7296/8735554985_e1d14c1b04_m.jpg)](http://www.flickr.com/photos/while42/8735554985)
    [![](http://farm8.staticflickr.com/7309/8735551177_e825f61638_m.jpg)](http://www.flickr.com/photos/while42/8735551177)  
    [![](http://farm9.staticflickr.com/8385/8601098548_86554f82bd_m.jpg)](http://www.flickr.com/photos/while42/8601098548)
    [![](http://farm9.staticflickr.com/8243/8572667262_f8cba606f3_m.jpg)](http://www.flickr.com/photos/while42/8572667262)  
    [![](http://farm9.staticflickr.com/8245/8498317615_d61db87b60_m.jpg)](http://www.flickr.com/photos/while42/8498317615)
    [![](http://farm9.staticflickr.com/8341/8286597568_c274d422da_m.jpg)](http://www.flickr.com/photos/while42/8286597568)  

    [More photos on Flickr](http://www.flickr.com/photos/while42/sets)

And this is included in both `index.md` and `fr/index.md`:
    {% raw %}
    Photos
    =======
    {% include home/photos.md %}
    {% endraw %}

Use of variables
===================

In templates, we can call variables defined either by the system, either by the configuration file `_config.yml`.

For example we have in the config file:

    twitter_username: while42

{% raw %}
So we can use it as `{{ site.twitter_username }}` in `_includes/footer.html`:
{% endraw %}

    {% raw %}
    {% if site.twitter_username %}
    <li>
      <a href="https://twitter.com/{{ site.twitter_username }}">
        <span class="icon icon--twitter">
          <svg viewBox="0 0 16 16">
            <path fill="#828282" d="M15.969,3.058c-0.586,0.26-1.217,0.436-1.878,0.515c0.675-0.405,1.194-1.045,1.438-1.809 c-0.632,0.375-1.332,0.647-2.076,0.793c-0.596-0.636-1.446-1.033-2.387-1.033c-1.806,0-3.27,1.464-3.27,3.27 c0,0.256,0.029,0.506,0.085,0.745C5.163,5.404,2.753,4.102,1.14,2.124C0.859,2.607,0.698,3.168,0.698,3.767 c0,1.134,0.577,2.135,1.455,2.722C1.616,6.472,1.112,6.325,0.671,6.08c0,0.014,0,0.027,0,0.041c0,1.584,1.127,2.906,2.623,3.206 C3.02,9.402,2.731,9.442,2.433,9.442c-0.211,0-0.416-0.021-0.615-0.059c0.416,1.299,1.624,2.245,3.055,2.271 c-1.119,0.877-2.529,1.4-4.061,1.4c-0.264,0-0.524-0.015-0.78-0.046c1.447,0.928,3.166,1.469,5.013,1.469 c6.015,0,9.304-4.983,9.304-9.304c0-0.142-0.003-0.283-0.009-0.423C14.976,4.29,15.531,3.714,15.969,3.058z"/>
          </svg>
        </span>

        <span class="username">{{ site.twitter_username }}</span>
      </a>
    </li>
    {% endif %}
    {% endraw %}

Data storage
=================

This is the most amazing part of recent Jekyll versions. You can store arbitrary Yaml files in `_data` and call them anywhere in the templates. So for the while42 website, for the right column where all the chapters are listed, I created a `_data/chapters/` with:

    _data/chapters/001_san_francisco.yml
    _data/chapters/002_new_york.yml
    _data/chapters/003_paris.yml
    _data/chapters/004_miami.yml
    _data/chapters/005_singapore.yml
    _data/chapters/006_bucharest.yml
    etc..

I gave them numbers to enforce the ordering with the `| sort` liquid filter below. In `_data/chapters/001_san_francisco.yml` we have:

    name: San Francisco
    country: us
    managers:

    - name: Julien Barbier
      image: julien.png
      linkedin: http://www.linkedin.com/in/julienbarbier
      facebook: http://www.facebook.com/julienbarbier42
      twitter: http://twitter.com/julienbarbier42

    - name: Sylvain Kalache
      image: sylvain-kalache.jpg
      linkedin: http://www.linkedin.com/in/sylvainkalache
      twitter: http://twitter.com/sylvainkalache

    - name: Florent Crivello
      image: florent.png
      linkedin: http://www.linkedin.com/pub/florent-crivello/52/901/64
      twitter: http://twitter.com/Altimor

This made possible to include this in the main template `_include/home.html`:

    {% raw %}
    {% assign allchapters = site.data.chapters | sort %}
    {% for chapter_hash in allchapters %}
    {% assign chapter = chapter_hash[1] %}
    <div class="module">
      <ul>
        <li>
          <h3>
            {% if chapter.country %}
            <img src="{{ site.baseurl }}/flags/{{ chapter.country }}.gif">
            {% endif %}
            root <b>@</b> {{ chapter.name }}
          </h3>
          <ul>
            {% for manager in chapter.managers %}
            <li>
            <img src="{{ site.baseurl }}/avatars/{{ manager.image }}" width="50" height="50" class="avatar" alt="photo">
            {{ manager.name }}<br>
            {% if manager.linkedin %}
            <a href="{{ manager.linkedin }}"><img src="{{ site.baseurl }}/icons/linkedin.png" width="16" height="16" alt="linkedin"></a>
            {% endif %}
            {% if manager.facebook %}
            <a href="{{ manager.facebook }}"><img src="{{ site.baseurl }}/icons/facebook.png" width="16" height="16" alt="facebook"></a>
            {% endif %}
            {% if manager.google %}
            <a href="{{ manager.google }}"><img src="{{ site.baseurl }}/icons/gplus.png" width="16" height="16" alt="google"></a>
            {% endif %}
            {% if manager.twitter %}
            <a href="{{ manager.twitter }}"><img src="{{ site.baseurl }}/icons/twitter.png" width="16" height="16" alt="twitter"></a>
            {% endif %}
            {% if manager.github %}
            <a href="{{ manager.github }}"><img src="{{ site.baseurl }}/icons/github.png" width="16" height="16" alt="github"></a>
            {% endif %}
            </li>
            {% endfor %}
          </ul>
        </li>
      </ul>
    </div>
    {% endfor %}
    {% endraw %}

The possibilities of liquid are a bit frustrating for a coder as there is no easy way to do a variable variable, but still, it does the job pretty well. In the data file we only need to declare the lines for social links we have, and the icon for it is only displayed if the value is there.

The main point here is that all the content of `_data` is usable in templates by just replacing the path slashes with dots. So I also made another `_data/chapters_to_be.yml` with:

    - La Réunion
    - Tours

And it's included in a much simpler way as:

    {% raw %}
    <p>
      These are the while42 chapters that members are creating right now:<br>
      {% for chapter_to_be in site.data.chapters_to_be %}
      - <b>{{ chapter_to_be }}</b><br>
      {% endfor %}
    </p>
    {% endraw %}


An optimal workflow
======================

The side effect of treating the while42 website as a public repository is that organizers from each chapters can now just clone the repo, add their modifications on their very own data file, and issue a pull request to the main repo. The websites maintainers (Julien and Sylvain) just have to check if the yaml file is properly formatted, if photos are added correctly, and they merge de pull request, then the website is updated.

It didn't wait long before we get the first pull requests on <https://github.com/while42-org/while42-org.github.io>:

- <https://github.com/while42-org/while42-org.github.io/pull/2>
- <https://github.com/while42-org/while42-org.github.io/pull/3>

More about static generation
==============================

For many webiste, that are not full-blown web applications, generating staticaly makes a lot of sense. Jekyll is a great tool but there are a whole lot of various frameworks in all possible languages that you can use for your own needs. Check out <http://www.staticgen.com> for a list of them.



[w42]:             http://while42.org
[gandi]:           https://gandi.net
[meeting]:         https://www.flickr.com/photos/while42/sets/72157648296590483/
[jekyll]:          http://jekyllrb.com
[gh-pages]:        https://pages.github.com/
[mojombo]:         https://github.com/mojombo
[jekyll-contribs]: https://github.com/jekyll/jekyll/graphs/contributors
[liquid]:          https://github.com/Shopify/liquid/wiki/Liquid-for-Designers
[dockerfile]:      https://github.com/while42-org/while42-org.github.io/blob/master/Dockerfile
