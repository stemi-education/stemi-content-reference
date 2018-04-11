# STEMI static content - Products & Adventures

Directory structure and dummy files should be considered as a reference implementation
of a human readable/editable and machine readable format for STEMI web static content.

Two main segments of the STEMI web content are Adventures and Products:

```
stemi-static-content-reference
|-- adventures
|-- products
```

They are divided into their respective directories. Rationale for this decision
stemmed from the mature wireframe for the STEMI web, showing STEMI product catalog
page, and their individual per product pages.

## Products

Each product should have a separate directory with `.json` file describing the
product, and media files in `assets` directory. The name of the product directory
defines the namespace for that product, that serves for URL generation and CDN
bucket placement for media files.

```
products
|-- hexapod2017
|   |-- assets
|   |   |-- cover.jpeg
|   |   |-- icon1.png
|   |   |-- icon2.png
|   |   |-- image1.png
|   |   |-- thumbnail.png
|   \-- product.json
|-- drone2017
.
```

`product.json` has a following structure:

```
{
  "name": "Hexapod",
  "type": "HARDWARE_KIT",  /* HARDWARE_ADDON or SOFT_ADVENTURE */
  "availability": "SOLD_OUT", /* AVAILABLE */
  "price": [
    {
      "currency": "USD",
      "amount": 249,
      "discount": 200   /* discount optional */
    },
    {
      "currency": "HRK",
      "amount": 1500,
      "discount": 1000
    }
  ],
  "thumbnail": "thumbnail.jpeg",
  "cover": "cover.jpeg",
  "tabs": [   /* tabs contain data for well defined React components */
    {
      "title": "Overview",
      "content": [
        {
          "type": "HeroImage",
          "props": {
            "imagePosition": "left",
            "imagePath": "image1.png",
            "title": "The journey start with a box",
            "description": "Sample description"
          }
        },
        {
          "type": "FeaturesList",
          "props": {
            "features": [
              {
                "icon": "icon1.png",
                "label": "56 lessons"
              },
              {
                "icon": "icon2.png",
                "label": "26 hours on-demand video"
              }
            ]
          }
        }
      ]
    },
    {
      "title": "Specification",
      "content": [...]
    },
    {
      "title": "What others say",
      "content": [...]
    }
  ]
}
```

## Adventures

Each adventure should have a separate directory containing the `.json` file describing
the adventure, directories with various courses for that specific adventure and `.md`
lesson files with the lesson content and metadata for the specific lesson.

Notice that there is an adventure `hexapod2017` - there will sometimes be products
that have the same name as the adventure, but that will not always be the case.
Sometimes the products will not have an adventure of their own, but will be a part
of some already existing adventure.

`course.json`  will be responsible for defining each course, and a field named `type`
will resolve if the course is the part of the base adventure (`BASE_COURSE`), or if it comes with additional products (`ADDITIONAL_COURSE`) the `course.json`.

```
adventuress
|--hexapod2017
|  |-- adventure.json
|  |-- assets
|  |   \-- cover.jpeg
|  |-- 01_3D_modeling
|  |   |-- course.json
|  |   |-- assets
|  |   |   \-- thumbnail.jpeg
|  |   |-- 01_getting_started
|  |   \-- 02_first_3D_model_servo_motor
|  |-- 02_second
|  .
.
```

Each course will have its own directory. Order of the courses and the lessons
will be defined by filenames, e.g. the courses, subsections and lessons will
preserve the alphabetical order as on the filesystem.

The course directory is defined by
  - `course.json`,
  - `assets` directory where all
the files such as media files or sourcecode files for education are located
(they can be referenced by links in lesson content for download)
  - subsection directories with lessons (subsection nesting is NOT allowed) and `lessons.json` file
  - only `.md` files and `lessons.json` are allowed in subsection directory
  - lessons in the form of `.md` files

  A typical course directory can be seen here:

```
01_3D_modeling
|-- course.json
|-- assets
|   \-- thumbnail.jpeg
|-- 01_getting_started
|   |-- lessons.json
|   |-- 0101_welcome.md
|   \-- 0102_installation.md
|-- 02_first_3D_model_servo_motor
|   |-- lessons.json
|   |-- 0201_first_servo1.md
|   \-- 0202_first_servo2.md
.
```

Pay special attention to directory and file naming - these names were chosen to
ensure the desired ordering of courses, subsections and lessons.

### Lesson.md format

Format of lesson files consists of mixture of [YAML](http://yaml.org/) and [Markdown](https://daringfireball.net/projects/markdown/). [YAML 1.2 spec](http://yaml.org/spec/1.2/spec.html#id2800132) defines three-hyphens (`---`) for delimiting
completely independent documents. We use this feature to delimit YAML with metadata from Markdown.

```
title: Modeling First Servo Motor - Part 1
id: !!str 619323
published: true
---

## Modeling First Servo Motor - Part 1

Hi guys! :)

We are now going to start 3D modeling. While doing so, we are going to get familiar
with the concept of 3D modeling and get to know a lot of new concepts and possibilities
that Fusion 360 offers.

/youtube s5BMMU3kzjs

We learnt:
* how to create __components__
* what are components
* some Fusion __shortcuts__ (for example C for the centre diameter circle, L for
line and so on) that save our time :)
```

Notice `/youtube` line in the middle of Markdown content. This is our internal
tag for embedding youtube videos in our educational content. This should be substituted
with appropriate HTML on either on backend or frontend.

_RESTRICTION_: This content specification allows only one video per lesson file. For
this reason we are considering moving the youtube video id in the YAML portion of
the file, and always rendering the video as first thing below the title of the lesson.

For now, only the `id` field in the YAML metadata is mandatory. `id` is used for
generating unique, static and standardised REST API for the lessons.

`published` tag marks the lesson visible or not visible on the production server.
Default value is `true`. If the value is `false` the lesson is only displayed on
the `dev` server.

Assets like media, sourcecode files etc. are placed _per course_ in the courses
`assets` directory. No assets or directories containing assets should be put in
course subdirectories.
