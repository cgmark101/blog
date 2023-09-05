# Akane-Blog

This is the source code for a blog, made with flask, it use a markdown pattern for the blog posts, can be dynamically
generated or statically build using frozen-flask

Go to [Akane.ga](https://akane.ga/articles/deta-blog/) for more info

This blog use a custom contact form backend and database hosted on [Deta](https://web.deta.sh) check the
[serverless-form-email](https://github.com/cgmark101/serverless-form-email) for more details

## How to use this:
```bash
$ git clone https://github.com/cgmark101/blog.git
$ cd blog
$ virtualenv .venv
$ pip install -r requirements.txt
$ python main.py
```

For optimized build:
```bash
$ python freeze.py
``` 

The static files are in the `build` folder

Add blog posts/articles in markdown sintax inside the pages folder and your done.

<p align="center">
    <a href="https://akane.ga" target="_blank"><img
            src="https://cdn.jsdelivr.net/gh/cgmark101/CDN-stuff@main/dist/img/akane.ga.jpg" alt="akane.ga"
            style="max-width:100%;"></a>
</p>

<p align="center">
    <a href="https://akane.ga" target="_blank"><img
            src="https://cdn.jsdelivr.net/gh/cgmark101/CDN-stuff@main/dist/img/akane.ga_(iPad).jpg"
            alt="akane.ga_mobile" width="500" /></a>
</p>
