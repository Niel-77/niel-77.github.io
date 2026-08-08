---
layout: archive
title: "CV"
permalink: /cv/
author_profile: true
redirect_from:
  - /resume
---

{% include base_path %}

Education
======
* **Ph.D. in Mechanical Engineering**
  * Texas A&M University, USA *(Ongoing)*
* **Bachelor's Degree in Engineering**
  * Institute of Engineering (IOE), Pulchowk Campus, Tribhuvan University, Nepal

Work & Research Experience
======
* **Graduate Research Assistant**
  * Texas A&M University
  * Focus: Design Innovation, Multi-Agent AI Systems, CAD Automation
* **Researcher / Engineer**
  * Accelerated Komputing

Skills
======
* **Programming & AI/ML**: Python, PyTorch, Deep Learning, Graph Neural Networks (GNNs), LLM Multi-Agent Systems
* **CAD, CAE & Simulation**: SolidWorks, Parametric CAD, Finite Element Method (FEM), Structural Simulation
* **Robotics & Automation**: ROS2, SLAM, Nav2, Autonomous Mobile Robots (AMRs)

Publications
======
{% assign sorted_publications = site.publications | sort: 'date' | reverse %}
<ul>
  {% for post in sorted_publications %}
    {% include archive-single-cv.html %}
  {% endfor %}
</ul>
