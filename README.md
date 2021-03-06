# Middleman::GoogleDrive

[![Gem Version](https://badge.fury.io/rb/middleman-google_drive.svg)](http://badge.fury.io/rb/middleman-google_drive)

This is an extension for Middleman that allows you to load data from a google
spreadsheet into your template data.

## Installation

Add this line to your application's Gemfile:

    gem 'middleman-google_drive'

And then execute:

    $ bundle install

Or install it yourself as:

    $ gem install middleman-google_drive

## Usage

The extension will get loaded automatically, you just need to activate it.

```ruby
activate :google_drive
```

### Spreadsheet

There are two ways to load and use spreadsheets. Most the time you just need a
single document with multiple worksheets. If you only need a single document...

```ruby
activate :google_drive, load_sheets: 'mygoogledocumentkey'
```

You can then use the worksheets in your templates (make sure your worksheets
have names that only contain alpha-numeric characters; no spaces or strange things).

```erb
<h1>My spreadsheet is called: <%= data.mysheet['title'] %></h1>
<% data.Sheet1.each do [row] %>
    Column one header: <%= row['Column one header'] %>
    Column two header: <%= row['Column two header'] %>
    My column name: <%= row['My column name'] %>
<% end %>
```

If you would like to load multiple documents from google, you can use a hash when
activating the extension:

```ruby
activate :google_drive, load_sheets: {
    mysheet: google_spreadsheet_key,
    moresheet: another_key
}
```

Then you can use the data from any of the loaded documents in your templates:

```erb
<h1>My spreadsheet is called: <%= data.mysheet['title'] %></h1>
<% data.mysheet['Sheet1'].each do [row] %>
    Column one header: <%= row['Column one header'] %>
    Column two header: <%= row['Column two header'] %>
    My column name: <%= row['My column name'] %>
<% end %>
<% data.moresheet['Sheet1'].each do [row] %>
    Column one header: <%= row['Column one header'] %>
    Column two header: <%= row['Column two header'] %>
    My column name: <%= row['My column name'] %>
<% end %>
```

### Documents

You can load documents similarly to spreadsheets. With documents, you have 
options: plain text, HTML or [ArchieML](http://archieml.org/).

To load a single document as text:

```ruby
activate :google_drive, load_docs: 'mygoogledocumentkey'
```

You can now access the text in `data.doc`. To change the name of the variable name
or to load multiple documents, use a hash:

```ruby
activate :google_drive, load_docs: {
    first_article: 'googledockeyone',
    second_article: 'googledockeytwo'
}
```

- To load HTML, use `load_docs_html` instead of `load_docs`.
- For [ArchieML](http://archieml.org/), use `load_docs_archieml`.

The HTML version of a Google doc will be a complete HTML document that includes
`<html>`, `<head>` and `<body>` tags. You'll probably wanna use the sanitize gem
to clean things up.

You can then access your document in your templates thusly:

```erb
<article>
<%=data.first_article %>
</article>
```

If you are loading ArchieML, the data will come in the form of a hash. Both text and html come as strings.

## Setup

The first time you use this extension, you will have to configure the authentication
with Google docs. There are two parts to this. First you will have to register
a client application with Google and get API keys. Tarbell has a [great
explanation](http://tarbell.readthedocs.org/en/latest/install.html#configure-google-spreadsheet-access-optional) on how to do this. You will need to copy the
`client_secrets.json` to `~/.google_client_secrets.json`. The first time you
run middleman, you will be sent to a Google login prompt in order to
associate Middleman with your Google account. You should make sure that the
account that you choose has access to the spreadsheets you want to use in
Middleman.

You need to make sure that the `redirect_urls` setting for the client secrets
is `urn:ietf:wg:oauth:2.0:oob` and not another URL.

Protip: If somebody in your org has used this extension or something like it,
chances are that person has already created a client secrets file that can be
shared with you.

Once you authenticate with Google, a new file `~/.google_drive_oauth2.json`
will be created containing an key for communicating with Google Drive.

You can override the location of the client secrets and oauth2 JSON files with
the environment variables `GOOGLE_CLIENT_SECRETS` and `GOOGLE_DRIVE_OAUTH`.

If you plan to run Middleman on a server, you can use Google's server to server
authentication. This will kick in if you define the environment variables
`GOOGLE_OAUTH_PERSON`, `GOOGLE_OAUTH_ISSUER` and either `GOOGLE_OAUTH_KEYFILE`
or `GOOGLE_OAUTH_PRIVATE_KEY`.

## Contributing

1. Fork it ( https://github.com/voxmedia/middleman-google_drive/fork )
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new Pull Request
