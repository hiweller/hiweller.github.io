---
# An instance of the Contact widget.
# Documentation: https://sourcethemes.com/academic/docs/page-builder/
widget: contact

# This file represents a page section.
headless: true

# Order that this section appears on the page.
weight: 130

title: Contact
subtitle:

content:
  # Automatically link email and phone or display as text?
  autolink: true
  
  # Email form provider
  form:
    provider: formspree
    formspree:
      id: test
    netlify:
      # Enable CAPTCHA challenge to reduce spam?
      captcha: false
  
design:
  columns: "1"
  background:
    image: moss_light.JPG
    image_darken: 1.1
    image_parallax: true
    image_position: center
    image_size: cover
    text_color_light: false
  spacing:
    padding: ["20px", "0", "20px", "0"]
---
