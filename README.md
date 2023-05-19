# Kubernetes slides

I have written these slides in many versions in many companies and each time I switch company I have to redo them.

No more! It's time to open-source the slides.

Not that I'm a slides pro but it's good to have a base.

I'm using [Marp](https://marp.app/) to generate the slides, another option is google workspace [md2googleslides](https://github.com/googleworkspace/md2googleslides).
There are probably a bunch of other [options](https://gist.github.com/johnloy/27dd124ad40e210e91c70dd1c24ac8c8) as well.

## Marp

Download the [marp-cli](https://github.com/marp-team/marp-cli).

## View slide

Marp supports html tags, if you want to support collums in your slides you will need to use a thrid-party that uses the html functions.

```shell
# Open a marp preview
marp hello.md --preview --html
```

You can also use the vscode extension.
Don't forget to enable the html functionality in the extension.
