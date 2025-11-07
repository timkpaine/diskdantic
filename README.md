## diskdantic

Disk-backed collections powered by Pydantic models.

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
    format="markdown",  # required when the folder is empty
    body_field="content",
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

### Nested folders

```python
from diskdantic import NestedCollection
from pydantic import BaseModel


class ShowInfo(BaseModel):
    slug: str
    title: str
    summary: str | None = None


class Episode(BaseModel):
    title: str
    content: str


shows = NestedCollection(
    parent_model=ShowInfo,
    child_model=Episode,
    root="./shows",
    parent_filename="info.yml",
    parent_format="yaml",
    child_format="markdown",
    child_body_field="content",
)

for record in shows:
    print(record.info.title, len(record.episodes))
```
