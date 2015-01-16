# NAME

Mojolicious::Plugin::Directory::Stylish - Serve static files from document root with directory index using Mojolicious templates

# VERSION

version 1.000

# SYNOPSIS

    use Mojolicious::Lite;
    plugin 'Directory::Stylish';
    app->start;

or

    > perl -Mojo -E 'a->plugin("Directory::Stylish")->start' daemon

# DESCRIPTION

[Mojolicious::Plugin::Directory::Stylish](https://metacpan.org/pod/Mojolicious::Plugin::Directory::Stylish) is a static file server directory index a la Apache's mod\_autoindex.

# METHODS

[Mojolicious::Plugin::Directory::Stylish](https://metacpan.org/pod/Mojolicious::Plugin::Directory::Stylish) inherits all methods from [Mojolicious::Plugin](https://metacpan.org/pod/Mojolicious::Plugin).

# OPTIONS

[Mojolicious::Plugin::Directory::Stylish](https://metacpan.org/pod/Mojolicious::Plugin::Directory::Stylish) supports the following options.

## `root`

    plugin 'Directory::Stylish' => { root => "/path/to/htdocs" };

Document root directory. Defaults to the current directory.

If root is a file, serve only root file.

## `auto_index`

    # Mojolicious::Lite
    plugin 'Directory::Stylish' => { auto_index => 0 };

Automatically generate index page for directory, default true.

## `dir_index`

    plugin 'Directory::Stylish' => { dir_index => [qw/index.html index.htm/] };

Like a Apache's DirectoryIndex directive.

## `dir_template`

    plugin 'Directory::Stylish' => { dir_template => 'index' };

    # with 'render_opts' option
    plugin 'Directory::Stylish' => {
        dir_template => 'index',
        render_opts  => { format => 'html', handler => 'ep' },
    };

    ...

    __DATA__

    @@ index.html.ep
    % layout 'default';
    % title 'DirectoryIndex';
    <h1>Index of <%= $current %></h1>
    <ul>
    % for my $file (@$files) {
    <li><a href='<%= $file->{url} %>'><%== $file->{name} %></a></li>
    % }

    @@ layouts/default.html.ep
    <!DOCTYPE html>
    <html>
      <head><title><%= title %></title></head>
      <body><%= content %></body>
      %= include $css;
    </html>

A name for the template to use for the index page.

"$files", "$current", and "$css" are passed in stash.

- $files: Array\[Hash\]

    list of files and directories

- $current: String

    current path

- $css: String

    name of template with css that you want to include

## `handler`

    use Text::Markdown qw{ markdown };
    use Path::Class;
    use Encode qw{ decode_utf8 };

    plugin 'Directory::Stylish' => {
        handler => sub {
            my ($c, $path) = @_;
            if ($path =~ /\.(md|mkdn)$/) {
                my $text = file($path)->slurp;
                my $html = markdown( decode_utf8($text) );
                $c->render( inline => $html );
            }
        }
    };

CODEREF for handle a request file.

If not rendered in CODEREF, serve as static file.

## `enable_json`

    # http://host/directory?format=json
    plugin 'Directory::Stylish' => { enable_json => 1 };

enable json response.

## `css`

    plugin 'Directory::Stylish' => { css => 'custom_template' };

    ...
    __DATA__

    @@ custom_template.html.ep
    <style type="text/css">
    body { background: black; color: white; }
    </style>

A name for the template with css that will be included by the default template
for the index.

This name will be available as `$css` in the stash.

# CONTRIBUTORS

Many thanks to the contributors for their work.

- ChinaXing
- Su-Shee

# SEE ALSO

- [Mojolicious::Plugin::Directory](https://metacpan.org/pod/Mojolicious::Plugin::Directory)
- [Plack::App::Directory](https://metacpan.org/pod/Plack::App::Directory)

# ORIGINAL AUTHOR

hayajo <hayajo@cpan.org> - Original author of [Mojolicious::Plugin::Directory](https://metacpan.org/pod/Mojolicious::Plugin::Directory)

# AUTHOR

Andreas Guldstrand <andreas.guldstrand@gmail.com>

# COPYRIGHT AND LICENSE

This software is copyright (c) 2015 by Hayato Imai, Andreas Guldstrand.

This is free software; you can redistribute it and/or modify it under
the same terms as the Perl 5 programming language system itself.
