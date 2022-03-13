---
layout: page
title: Projects
comments: false
permalink: /projects/
---

<style>
    /* Create two equal columns that floats next to each other */
    .column {
    position: relative;
    float: left;
    width: 50%;
    height: auto;
    padding: 5px;
    }

    /* Clear floats after the columns */
    .row:after {
    content: "";
    display: table;
    clear: both;
    }

    /* Responsive layout - makes the two columns stack on top of each other instead of next to each other */
    @media screen and (max-width: 640px) {
        .column {
            float: center;
            width: 100%;
        }
    }

    /* for the picture overlay */
    .column > img {
    width: 100%;
    float: left;
    height: 100%;
    }

    .img-overlay {
    width: 100%;
    height: 100%;
    background: rgba(0,0,0,0);
    color: rgba(255,255,255,0);
    position: absolute;
    top:0; left:0;
    transition: all .75s ease;
    }

    .text {
    color: white;
    opacity: 0;
    font-size: 24px;
    font-weight: bold;
    position: absolute;
    top: 50%;
    left: 50%;
    -webkit-transform: translate(-50%, -50%);
    -ms-transform: translate(-50%, -50%);
    transform: translate(-50%, -50%);
    text-align: center;
    }

    .img-overlay:hover {
    background: rgba(0,0,0,.8);
    color: rgba(255,255,255,1);
    transition: all .75s ease;
    }

    .img-overlay:hover .text {
    opacity: 1;
    transition: all .75s ease;
    }
</style>

<div class="row">
    <div class="column">
        <img src="/images/projects-everyday-titans-podcast-cover.png" alt="Avatar" class="image" style="width:100%">
        <a href="{{ site.baseurl }}/everydaytitans">
        <div class="img-overlay">
            <p class="text">Everyday Titans Podcast Show</p>
        </div>
        </a>
    </div>
    <div class="column">
        <img src="/images/projects-enigma-cover.png" alt="Avatar" class="image" style="width:100%">
        <a href="{{ site.baseurl }}/enigma">
        <div class="img-overlay">
            <p class="text">Enigma - Client-Side Web App For RSA Public Key Cryptography System</p>
        </div>
        </a>
    </div>
    <div class="column">
        <img src="/images/projects-spawnlab-cover.png" alt="Avatar" class="image" style="width:100%">
        <a href="https://spawnlab.ca/">
        <div class="img-overlay">
            <p class="text">SpawnLab - Spawning The Future 🌱</p>
        </div>
        </a>
    </div>
    <div class="column">
        <img src="/images/projects-jjunkyard-robotics-cover.png" alt="Avatar" class="image" style="width:100%">
        <a href="https://github.com/jjunkyard">
        <div class="img-overlay">
            <p class="text">Jjunkyard - Tatooine Droids Reloaded 🤖</p>
        </div>
        </a>
    </div>
</div>
