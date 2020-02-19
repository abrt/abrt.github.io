# The blog
This is the source code of ABRT's blog. You can visit it on
[https://abrt.github.io](http://abrt.github.io).

# How to write a new article
1. Write the article
2. Create a new file in the `/_post` directory that follows the convention `YYYY-MM-DD-name-of-post.markdown`
3. The file consists of two parts separated by `---`

    ```
    ---
    layout: post
    title:  "Title of your post"
    date:   2017-01-22 18:48:44 +0100
    author: <Your Name>
    visible: 1
    categories: <space-separated list>
    ---
    Put your article in Markdown format here.
    ```

Don't forget to change the title, date, author and categories

5. Put your article into the file and style it properly
5. Test what your article will really look like.
6. Create commit and push to the master branch or, preferably, push to your fork and open a pull request, allowing others to comment.

# How to style the article
Articles are written in the Markdown format. For inspiration on using Markdown,
see demo article in `inspiration_blog/2017-01-22-inspiration-blog.markdown` and
see it rendered [here](http://abrt.github.io/the/inspiration/2017/01/22/inspiration-blog/).

# Testing
When writing code it is natural to make errors. And since creating a new blog is
*"writing code"* you can make some as well. Therefore before pushing your article
you should check three things:

1. Article does not contain grammatical and typing errors
2. Article is displayed
  * ...as the newest article
  * ...with correct title and date
3. Article is styled correctly

Testing the formatting and layout can be tricky, but thankfully there is a easy way to do so.
First, you will need to install some Ruby gems. Please run the following in the source root directory:

    # dnf install rubygem-jekyll ruby-devel
    $ bundle install

Then you should be ready to run:

    $ bundle exec jekyll serve

This will deploy the whole website locally. Click a link provided by the above command
(probably [http://localhost:4000](http://localhost:4000))

Now you can see the front page. Make sure your article is on the top, click it and
check it.

When you make changes, there is no need to restart the local server - just let
it run and when file is edited, it is uploaded automatically. You only need to
refresh the site in your browser.

## Credits
[Original theme](https://startbootstrap.com/template-overviews/clean-blog/) - modified a bit
