---
title: 'Projects'
date: 2024-05-19
type: landing

design:
  spacing: '5rem'

sections:
  - block: collection
    content:
      title: Selected Projects
      text: I've been a maintainer of OSS projects for more than a decade. Below you can find a list of selected projects that I've been working on during that time.
      filters:
        folders: [project]
      count: 25              # ← show up to 25 tiles on the main page
      # offset: 0
      # sort_by: 'Date'
      # sort_ascending: false
      archive:
        enable: true         # optional “See all projects” link
        text: See all
        link: project/
    design:
      view: article-grid
      fill_image: false
      columns: 3
      show_date: false
      show_read_time: false
      show_read_more: false
---
