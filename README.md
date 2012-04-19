plink
=====

Super lightweight PHP templating tool that uses pure PHP syntax.
No need to compile files into PHP.

Blocks
====

<p>Blocks are sections of layout that you can define and then use later.
Defining blocks for use in the layout clear and simple.
Enclose your blocks in <code>$this->block('name here')</code> and <code>$this->endblock()</code>: </p>
<pre class="brush: php">
&lt;?php $this->block('title') ?>
Welcome to my site!
&lt;?php $this->endblock() ?>

//this does the same thing
$this->block('title', 'Welcome to my site!'); //shortcut for small blocks
</pre>
<p>You can access blocks by using $this as an array.  To output the block defined above: </p>
<pre class="brush: php">
&lt;title>&lt;?=$this['title'] ?>&lt;/title>
</pre>
<p>You can use if structures to set a default block to use if a block is not defined: </p>
<pre class="brush: php">
&lt;title>
&lt;?php if(!$this['title']): ?>
No title :(
&lt;?php else: echo $this['title']; endif; ?>
&lt;/title>
</pre>

Template Inheritance
====

<p>So what is the point of blocks?  Blocks are useful because we can define blocks in one template,
	 then <em>extend</em> another one and use it there!
	Template inheritance is extremely powerful.  Extending a template is easy: </p>
<pre class="brush: php">
&lt;?php $this->extend('layout.php'); ?>
This is my content.
</pre>
<p>Let's make up some terms so you can understand the next paragraph.
We'll nickname the template we're extending the parent.
The template extending the parent will be called the child.  Think OOP if you're familiar with that.</p>
<p>When a template extends another one, any content that is not in a block in the child will become the content block in the parent.
	As well, all blocks defined in the child will be available in the parent.</p>
<p>If the parent template is extending another template (let's call that the grandparent), then any non-block content in the parent will be appended to the
content block in the child.  This new content block will be the content block of the grandparent.</p>
<p>For example: </p>
<pre class="brush: php">
//child.php
&lt;?php $this->extend('parent.php') ?>
Welcome to my site!

//in parent.php
echo $this['content'];
//will output 'Welcome to my site!'
</pre>
<p>It is not a good idea to name a block <em>content</em>.</p>

Nameless Blocks
====

<p>A block with no name will be outputted when you call <code>$this->endblock()</code>.
That sounds pretty useless but it's not if you pass in a parameter to endblock.</p>
<pre class="brush: php">
&lt;?php $this->block() ?>
&lt;script>alert('I am dangerous code!')&lt;/script>
&lt;?php $this->endblock(self::ESCAPE) ?>

//this will do the same as above
&lt;?php $this->block() ?>
&lt;script>alert('I am dangerous code!')&lt;/script>
&lt;?php $this->endescape() ?>
</pre>
<p>This will output the block escaped.  If you try <code>endblock(self::ESCAPE)</code> with a named block,
it will also be escaped but not outputted.</p>
<p>A block with no name and no parameters passed to endblock is not useful!</p>

Output Escaping
====
<p>You can escape blocks of output easily: </p>
<pre class="brush: php">
echo $this['title']->escape();
echo $this['title']->e(); //shortcut
</pre>
<p>Escape variables with h(): </p>
<pre class="brush: php">
echo h($dangerous);
</pre>
<p>Make sure you are not escaping output twice by <code>endblock(self::ESCAPE)</code> and then by <code>$this['block']->escape()</code></p>

Setup
=====

Create an Environment, pass in your template directory and you're ready to render templates.
The Environment's render() function returns the template as a string:
<pre>
use Dewdrop\Environment;

$dew = new Environment('path/to/templates');
echo $dew->render('index.php', array('pass'=>$template, 'variables'=>$here));
</pre>

The Environment can hold variables shared by all your templates such as helpers.  Set variables like this:

<pre>
$dew->helper = new Helper();
$dew->favouriteColour = "green";
</pre>

Now, in your template, you can use your Helper object and your favouriteColour.

<pre>
My favourite colour is &lt;?php echo $this->favouritecolour ?>.
&lt;?php echo $this->helper->link('Click here', 'rabbit.html') ?> to see my pet rabbit!
</pre>