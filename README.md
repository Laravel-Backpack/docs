# Backpack for Laravel Documentation

You can find the online version of the Backpack documentation at https://backpackforlaravel.com/docs

### How it works
- the ```master``` branch is the one showing up on https://backpackforlaravel.com/docs
- inside each version folder there's an index.md file, where we store the sidebar menu for that version
- inside each version folder there's an introduction.md file, which is the home page for that version

### Contribution guidelines
- pull requests are welcome and appreciated;
- pull requests should point to the ```master``` branch;
- all links should point to the current version of the documentation page (ex: ```/docs/{{version}}/installation```);
- all images should be uploaded to ```http://backpackforlaravel.com/uploads/docs/``` and used from there; ask @tabacitu to help upload them;
- all headings should be prepended by an HTML anchor, with the name of the heading; this way, Algolia can take the user directly to that heading (ex: ```<a name="what-my-title-says-but-with-dashes"></a>```);