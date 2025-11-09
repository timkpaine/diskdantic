## diskdantic

> Instead of having an ORM on top of a database, why not have a collection on top of a folder?

Disk-backed collections powered by Pydantic models. This is pretty much the whole API:

```python
from datetime import date
from pydantic import BaseModel
from diskdantic import Collection


class BlogPost(BaseModel):
    title: str
    date: date
    tags: list[str]
    draft: bool = False
    content: str


posts = Collection(
    BlogPost,
    path="./blog/posts",
    format="markdown",    # required when the folder is empty
    body_field="content", # required when format is markdown (for the body)
)

recent = posts.filter(lambda post: not post.draft).order_by("-date").head(3)
for post in recent:
    print(post.title)

new_post = BlogPost(
    title="Hello World",
    date=date.today(),
    tags=["intro"],
    content="# Hello\n\nIt works!",
)
posts.add(new_post)
```

It's meant to work with markdown files, but it should also work with yaml/json. 

## Why? 

It makes it easier to write a custom CMS on top of your disk, which is nice. But it also feels like a fun thing that should exist. It's mainly a fun brainfart for now, but I can see some areas where I might make it better too. 

1. Figure out a nice API for a nested collection. The library has one now, but undocumented for a reason.
2. Maybe make it more performant by seeing how far I can push the lazy loading. Though I doubt this will be worth it. 

