---
title: "Design Systems in Flutter: Today, Tomorrow, and Beyond"
datePublished: Wed Nov 19 2025 06:01:29 GMT+0000 (Coordinated Universal Time)
cuid: cmi5lh0s0000302l5hryfakb2
slug: design-systems-in-flutter-talk
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1763528453930/283b8047-3b0e-49e1-bb16-0ca6625b57d9.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1763532072232/9aaa30dd-f1d1-4c05-9e4d-ef7cedbcef38.jpeg
tags: flutter, community, gdg, techtalks

---

At **GDG DevFest Hanoi** on 15 November 2025, I had the opportunity to share a topic about **“Design Systems in Flutter: Today, Tomorrow, and Beyond**”.

It was an energizing room, with many developers and curious people wanting to understand how UI, frameworks, and design methodology are evolving together. In this post, I’d like to wrap up the session highlights and capture what we explored during the talk.

## 🧩 1. What is a Design System?

I kicked off the session with the basics:  
**What exactly is a design system?**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763524289309/57a132b4-fcee-403d-ad3c-b2fa4056c548.png align="center")

Referring to **Figma’s** [**blog post**](https://www.figma.com/blog/design-systems-101-what-is-a-design-system/#design-systems-and-ux-more-than-just-aesthetics), we explored design systems as a shared language - a combination of **principles, patterns, components, tokens, and documentation** that help teams build consistent interfaces faster and with fewer decisions.

Beyond being a repository of reusable pieces, design systems are about alignment - reducing ambiguity, increasing velocity, and keeping products visually coherent even as teams scale.

## 🎨 2. Material Design as an example

To ground the concept, I walked through one of the most influential design systems of our era: **Material Design**.

We explored:

* Its **history**, from 2014 to 2025
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763524399982/628ac455-06aa-43a3-82e5-7f0c3d5655f5.png align="center")
    
* It also came along with introduction videos showing the evolution through Material 1 → Material 2 → Material 3 → Material 3 Expressive
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763524422290/b28a92d6-c0d8-4a31-a083-d3396bccfccd.png align="center")
    

Material is more than just a theme - it’s a milestone in unifying design language across platforms, and it helps explain why design systems matter at framework level.

## 3\. Introducing “Decoupling Design in Flutter”

This was the core of the talk: an inside look at the **“Decoupling Design in Flutter”** project currently being started and actively worked on by Flutter team.

### A fun [demo](https://df2025demo.codemagic.app) showing what built-in design systems in Flutter look like:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763528217042/7fc58cf8-3aaf-48e8-9a16-98b7a7f80957.gif align="center")

### Why the decoupling project exists

I shared the context that led to this initiative:

* Flutter now spans **web, desktop** platforms, but maintaining built-in design libraries inside the SDK doesn't scale well.
    
* The community increasingly asks for support for **third-party design systems** like Fluent UI, Yaru, Shadcn, Hux, Moon, and more.
    

The arrival of **Material 3 Expressive (May 2025)** and Apple’s **iOS 26 “Liquid Glass”** design direction signaled a shift - UI design is becoming more dynamic and expressive than ever.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763528028920/d2f0d6eb-06be-4b15-8e63-454ab82154cc.jpeg align="center")

### The 3 Core Problems Today

I highlighted three major limitations in the current Flutter UI architecture:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763528390753/ab8cf20d-2c93-42d4-86e6-9b6f8504850b.jpeg align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763527911134/7b60ffa9-6000-44d2-8b3c-212ea5c7f4e9.jpeg align="center")

#### **1\. Forced Migrations**

Big UI shifts- like Material 2 → Material 3 often require massive refactors just to get essential fixes or platform support. Teams and developers pay a high maintenance cost.

#### **2\. Slow Updates**

Material and Cupertino updates are tied to Flutter’s quarterly releases. This delays bug fixes, design refinements, and feature updates that users expect sooner.

#### **3\. Custom UI Limits**

Flutter’s core logic is tightly bound to Material, making custom design systems harder to build. Developers often end up duplicating components or losing accessibility features.

These problems become more visible while the ecosystem grows.

---

### 🎯 Project Goals & Targets

The Flutter team aims to solve these issues with a long-term architectural effort.

Key goals:

* **More flexibility**: Let teams choose and swap design libraries without heavy refactors.
    
* **Faster updates**: Material and Cupertino shouldn’t be locked to framework & engine releases.
    
* **Lightweight framework**: Reduce the core SDK’s size and responsibility.
    
* **Better foundations**: Invest in the core widget library so design systems can layer on top cleanly.
    

### 📦 What’s Changing?

The big shift:  
**Material and Cupertino will be moved out of Flutter’s core SDK.**

They will live as **standalone first-party packages** in [`flutter/packages`](https://github.com/flutter/packages) repo and on [pub.dev](http://pub.dev).  
This makes the framework:

* lighter
    
* more modular
    
* able to iterate faster
    
* friendlier to 3rd-party design systems
    

Alongside that, Flutter is significantly investing in the **core widgets library**, making it a stronger foundation for any design system.

### Project Timeline

I shared the plan announced by the Flutter team:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763528607169/fa36ea73-43f2-4f64-8451-c881fc66ef3a.png align="center")

* Phase 1 (from July - End 2025):
    
    * Reinforcement - A Better Foundation Critical Pre-Decoupling Refactoring
        
    * Ongoing Reinforcement and Community Collaboration
        
* Phase 2 (Early 2026)**:** Relocation - Establishing New Packages and a Faster Release Process
    
* Phase 3 (Late 2026 and Beyond)**:** Removal: A Phased and Predictable Deprecation
    

This is a multi-year effort, and when completed, apps will need to **migrate to use external Material/Cupertino packages** but with clear guides and tooling to support the transition.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763526707141/e6b13d2d-1ca1-4c57-8c22-28f218b28144.png align="center")

## 4\. Community-Built Design Systems

After discussing the upcoming changes, I showcased examples of thriving community design systems, including demos and visuals.

These included:

* Fluent UI
    
* Yaru
    
* Shadcn
    
* Hux
    
* Moon
    
* And several experimental or indie design libraries
    

The takeaway:  
Flutter’s future UI ecosystem will be more modular, expressive, and diverse than ever.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763527269700/a7cb9cf7-39e7-46af-b131-27119b9138b9.jpeg align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763527243399/fe64e0c4-cd7e-4b03-95b6-682b4071ec0e.jpeg align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763527275338/a427dbe7-85a3-4565-977e-059591a20dcc.jpeg align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763527283577/db819b4e-83d6-44a4-b645-bd753af65019.jpeg align="center")

## 5\. Introducing Flutter Phở community 🇻🇳

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763527439097/dadbe02a-21d5-428e-bf63-ad9336656403.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763527500068/1b19a8fa-77b6-40eb-8608-14d5947e8d6d.png align="center")

One highlight of my talk was sharing about [**Flutter Phở**](https://www.facebook.com/groups/fluttervietnam), a new community initiative in Vietnam.

I explained:

* Why the community was started
    
* The desire to bring local meetups and events similar to global Flutter communities
    
* The goal of building a space where Vietnamese Flutter developers can connect, learn, and grow together
    

The response from the audience was incredibly encouraging. Many developers asked how they can join future events, exactly the kind of energy that sparks a strong community.

## 6\. A Little Fun: Game time

To close the session, we played a quick Kahoot quiz, equal parts fun and chaotic 😄  
It was a great way to test what people learned, spark laughs, and end the session with high spirits before moving into networking.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763527657915/c7f6d81a-bed1-4347-a996-86c6dea6a74d.jpeg align="center")

**Disclaimer:** Everything in this talk comes from Flutter’s public documents and [design](http://flutter.dev/go/decouple-design) proposals. I’m not speaking on behalf of the Flutter team, just sharing what’s publicly known.

I also expect to listen to all your [feedback](https://docs.google.com/forms/d/1EDPH4qA82_AxmnsWjTuVUVEEv-bh5Zwj-ZGI_VqYCIA/edit) and questions, if any:

Feel free to look again at my slide for the talk [here](https://docs.google.com/presentation/d/1b-8wf0A77Vxf2L9IVUpKe5Nsr-F0kfwEJWL_hEhGGDA/edit?usp=sharing).

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763528800108/c878dd5c-9f3f-4b57-a9e3-dab621a0a1ab.png align="center")