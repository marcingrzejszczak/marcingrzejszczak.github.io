---
title: Teaching & Consulting
summary: Mentoring, company consulting, and workshops.
type: landing
sections:
  - block: hero
    content:
      title: Mentoring, Consulting & Workshops
      text: |
        Level up CI/CD, Microservices, IDP, Testing, Observability.
         <img src="/images/consulting/devoxx.jpg" alt="Devoxx presentation" class="rounded-xl mt-6 mx-auto">        
    design:
      background:
        color: lights

  - block: markdown
    content:
      title: How I can help
      text: |
        <style>
          .consulting-metrics{display:grid;grid-template-columns:repeat(2,minmax(0,1fr));gap:2rem;margin:2rem 0;}
          .consulting-metric{display:flex;flex-direction:column;align-items:center;justify-content:center;padding:1.25rem;border-radius:1rem;border:1px solid var(--hb-border,#e5e7eb);background:var(--hb-card,#fff)}
          .consulting-metric .num{font-size:2rem;line-height:1.1;font-weight:800}
          .consulting-metric .label{opacity:.7;margin-top:.25rem;font-size:.9rem}
          @media (prefers-color-scheme: dark){
            .consulting-metric{border-color: #667995;background: #689dca;
            }
          }
        </style>
        <div class="consulting-metrics">
          <div class="consulting-metric">
              <div class="num">30+</div>
              <div class="label">Free Consultations</div>
          </div>
          <div class="consulting-metric">
              <div class="num">8</div>
              <div class="label">Mentees</div>
          </div>
        </div>
        I work with teams on delivery speed and reliability: CI/CD, microservices, internal developer platforms,
        testing strategy, and observability. Pick a service below to learn more and purchase or get in touch.
    design:
      background:
        color: light

  - block: collection
    id: services
    content:
      title: Services
      filters:
        folders: [ service ]
      count: 0
    design:
      view: article-grid
      fill_image: true
      columns: 2
      
  - block: markdown
    content:
      title: What clients say
      text: |        
        <div class="not-prose max-w-none">
          <style>.tms-wrap{position:relative}.tms-track{display:flex;gap:1.5rem;overflow-x:auto;scroll-snap-type:x mandatory;padding-bottom:.5rem;scroll-behavior:smooth}.tms-slide{flex:0 0 99%;scroll-snap-align:center}.tms-card{max-width:48rem;margin:0 auto}.tms-arrows{display:flex;justify-content:center;gap:.75rem;margin-top:.5rem}</style>
          <div class="tms-wrap">
            <div id="tms-track" class="tms-track">
              <div class="tms-slide">
                <article class="hb-card p-6 text-center tms-card">
                  <img src="/images/testimonials/enis.jpg" alt="Enis Halilaj" class="w-16 h-16 rounded-full object-cover mx-auto mb-3">
                  <p class="text-lg mb-2">“Thank you for the insightful conversation and valuable advice!”</p>
                  <div class="opacity-70">— Enis Halilaj, Software Developer @ Evonem</div>
                </article>
              </div>
              <div class="tms-slide">
                <article class="hb-card p-6 text-center tms-card">
                  <img src="/images/testimonials/mikiyas.jpg" alt="Mikiyas Eshetu" class="w-16 h-16 rounded-full object-cover mx-auto mb-3">
                  <p class="text-lg mb-2">“We laid out a plan that fits both the topics I want to explore and the way I like to learn. While working on my first sprint, one challenge has already made me rethink how I approach modularity and domain boundaries in Spring apps.”</p>
                  <div class="opacity-70">— Mikiyas Eshetu, Software Developer</div>
                </article>
              </div>
              <div class="tms-slide">
                <article class="hb-card p-6 text-center tms-card">
                  <img src="/images/testimonials/chris.jpg" alt="Chris Suszyński" class="w-16 h-16 rounded-full object-cover mx-auto mb-3">
                  <p class="text-lg mb-2">“The conversation with Marcin Grzejszczak was a blast, as always! We spoke about Event Mesh (...). Marcin picked up on the topic right away and sold me on some very interesting ideas! I highly recommend Marcin's consultations.”</p>
                  <div class="opacity-70">— Chris Suszyński, Senior Developer @ Red Hat</div>
                </article>
              </div>
              <div class="tms-slide">
                <article class="hb-card p-6 text-center tms-card">
                  <img src="/images/testimonials/mikolaj.png" alt="Mikołaj Telus" class="w-16 h-16 rounded-full object-cover mx-auto mb-3">
                  <p class="text-lg mb-2">“I expected to receive a huge dose of knowledge about architecture and Spring, but what struck me most was his extraordinary kindness and lack of any barriers.”</p>
                  <div class="opacity-70">— Mikołaj Telus, Java Developer</div>
                </article>
              </div>
              <div class="tms-slide">
                <article class="hb-card p-6 text-center tms-card">
                  <img src="/images/testimonials/emanuel.jpg" alt="Emanuel Trandafir" class="w-16 h-16 rounded-full object-cover mx-auto mb-3">
                  <p class="text-lg mb-2">“Had a great chat today with Marcin Grzejszczak — if you have a chance for conversations like this, take it. Totally worth it!”</p>
                  <div class="opacity-70">— Emanuel Trandafir, Senior Java Developer</div>
                </article>
              </div>
              <div class="tms-slide">
                <article class="hb-card p-6 text-center tms-card">
                  <img src="/images/testimonials/kamil.jpg" alt="Kamil Trepczyński" class="w-16 h-16 rounded-full object-cover mx-auto mb-3">
                  <p class="text-lg mb-2">“After years in leadership, I felt my tech edge fading. Rebuilding with Marcin’s mentorship — highly recommended.”</p>
                  <div class="opacity-70">— Kamil Trepczyński, Development Team Lead @ GFT Group</div>
                </article>
              </div>
              <div class="tms-slide">
                <article class="hb-card p-6 text-center tms-card">
                  <img src="/images/testimonials/bartlomiej.jpg" alt="Bartłomiej Kalka" class="w-16 h-16 rounded-full object-cover mx-auto mb-3">
                  <p class="text-lg mb-2">“I just had a great conversation with Marcin Grzejszczak, and I highly recommend such a consultation. Marcin answered my current questions about career building, development as a programmer, and the IT industry in general, including Spring. If you have the opportunity, it's worth taking advantage of his knowledge :)”</p>
                  <div class="opacity-70">— Bartłomiej Kalka, SoftwareEngineer@GetInt.io & Mentor@JavaReady.pl</div>
                </article>
              </div>
            </div>
            <div class="tms-arrows">
              <button id="tms-prev" class="hb-btn hb-btn-dark text-base px-4 py-2" type="button">‹ Prev</button>
              <button id="tms-next" class="hb-btn hb-btn-dark text-base px-4 py-2" type="button">Next ›</button>
            </div>
          </div>
          <script>(function(){const t=document.getElementById('tms-track');if(!t)return;const prev=document.getElementById('tms-prev');const next=document.getElementById('tms-next');function step(dir){const w=t.clientWidth;const gap=parseFloat(getComputedStyle(t).columnGap||getComputedStyle(t).gap)||24;const amount=Math.max(200,Math.floor(w*0.9)+gap);t.scrollBy({left:dir*amount,behavior:'smooth'});}prev&&prev.addEventListener('click',()=>step(-1));next&&next.addEventListener('click',()=>step(1));})();</script>
        </div>
    design:
      background:
        color: light


---
