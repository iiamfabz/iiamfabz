from pathlib import Path
import zipfile
import textwrap

base = Path("/mnt/data/fabri-github-portfolio")
assets = base / "assets"
assets.mkdir(parents=True, exist_ok=True)

html = r'''<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />

  <title>Fabri | Sistemas, Automatización e IA</title>
  <meta
    name="description"
    content="Portafolio de Fabri: estudiante de Ingeniería de Sistemas enfocado en automatización, IA aplicada a negocios y productos digitales."
  />

  <style>
    :root {
      --bg: #07080d;
      --bg-soft: #0d1019;
      --card: rgba(18, 21, 33, 0.76);
      --card-solid: #121521;
      --text: #f5f7ff;
      --muted: #9ca5ba;
      --line: rgba(255, 255, 255, 0.10);
      --primary: #8b5cf6;
      --secondary: #22d3ee;
      --success: #5ee1a2;
      --shadow: 0 24px 80px rgba(0, 0, 0, 0.42);
      --radius-lg: 28px;
      --radius-md: 20px;
      --radius-sm: 14px;
      --max-width: 1180px;
    }

    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
    }

    html {
      scroll-behavior: smooth;
    }

    body {
      min-height: 100vh;
      overflow-x: hidden;
      background:
        radial-gradient(circle at 10% 0%, rgba(139, 92, 246, 0.16), transparent 31rem),
        radial-gradient(circle at 90% 20%, rgba(34, 211, 238, 0.11), transparent 30rem),
        var(--bg);
      color: var(--text);
      font-family:
        Inter, ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont,
        "Segoe UI", sans-serif;
      line-height: 1.6;
    }

    body::before {
      position: fixed;
      z-index: -1;
      inset: 0;
      background-image:
        linear-gradient(rgba(255, 255, 255, 0.018) 1px, transparent 1px),
        linear-gradient(90deg, rgba(255, 255, 255, 0.018) 1px, transparent 1px);
      background-size: 46px 46px;
      mask-image: linear-gradient(to bottom, black, transparent 88%);
      content: "";
      pointer-events: none;
    }

    a {
      color: inherit;
      text-decoration: none;
    }

    img {
      display: block;
      width: 100%;
    }

    button,
    a {
      -webkit-tap-highlight-color: transparent;
    }

    .container {
      width: min(calc(100% - 40px), var(--max-width));
      margin-inline: auto;
    }

    .section {
      padding: 104px 0;
    }

    .eyebrow {
      display: inline-flex;
      align-items: center;
      gap: 9px;
      margin-bottom: 16px;
      color: var(--secondary);
      font-size: 0.78rem;
      font-weight: 800;
      letter-spacing: 0.14em;
      text-transform: uppercase;
    }

    .eyebrow::before {
      width: 28px;
      height: 1px;
      background: currentColor;
      content: "";
    }

    .section-heading {
      max-width: 740px;
      margin-bottom: 42px;
    }

    .section-heading h2 {
      margin-bottom: 14px;
      font-size: clamp(2rem, 5vw, 3.6rem);
      line-height: 1.05;
      letter-spacing: -0.055em;
    }

    .section-heading p {
      color: var(--muted);
      font-size: 1.04rem;
    }

    .gradient-text {
      color: transparent;
      background: linear-gradient(120deg, #ffffff 4%, #b6a1ff 48%, #61e7ff);
      background-clip: text;
      -webkit-background-clip: text;
    }

    /* Navigation */
    .nav-shell {
      position: fixed;
      z-index: 100;
      top: 18px;
      right: 0;
      left: 0;
      width: min(calc(100% - 28px), 1060px);
      margin: auto;
      border: 1px solid var(--line);
      border-radius: 999px;
      background: rgba(9, 11, 18, 0.74);
      box-shadow: 0 18px 50px rgba(0, 0, 0, 0.28);
      backdrop-filter: blur(18px);
    }

    .nav {
      display: flex;
      align-items: center;
      justify-content: space-between;
      min-height: 64px;
      padding: 0 14px 0 22px;
    }

    .brand {
      display: inline-flex;
      align-items: center;
      gap: 11px;
      font-weight: 850;
      letter-spacing: -0.03em;
    }

    .brand-mark {
      display: grid;
      width: 34px;
      height: 34px;
      place-items: center;
      border: 1px solid rgba(255, 255, 255, 0.14);
      border-radius: 11px;
      background: linear-gradient(135deg, var(--primary), var(--secondary));
      box-shadow: 0 8px 26px rgba(139, 92, 246, 0.34);
      color: #071014;
      font-weight: 950;
    }

    .nav-links {
      display: flex;
      align-items: center;
      gap: 5px;
      list-style: none;
    }

    .nav-links a {
      display: block;
      padding: 9px 13px;
      border-radius: 999px;
      color: var(--muted);
      font-size: 0.88rem;
      font-weight: 700;
      transition: 180ms ease;
    }

    .nav-links a:hover {
      background: rgba(255, 255, 255, 0.07);
      color: var(--text);
    }

    .nav-cta {
      display: inline-flex;
      align-items: center;
      gap: 8px;
      padding: 10px 15px;
      border: 1px solid rgba(255, 255, 255, 0.13);
      border-radius: 999px;
      background: rgba(255, 255, 255, 0.07);
      font-size: 0.86rem;
      font-weight: 800;
      transition: 180ms ease;
    }

    .nav-cta:hover {
      background: rgba(255, 255, 255, 0.12);
      transform: translateY(-1px);
    }

    /* Hero */
    .hero {
      display: grid;
      min-height: 100vh;
      align-items: center;
      padding: 132px 0 72px;
    }

    .hero-grid {
      display: grid;
      grid-template-columns: minmax(0, 1.15fr) minmax(310px, 0.85fr);
      gap: clamp(42px, 8vw, 100px);
      align-items: center;
    }

    .status {
      display: inline-flex;
      align-items: center;
      gap: 10px;
      margin-bottom: 24px;
      padding: 8px 13px;
      border: 1px solid rgba(94, 225, 162, 0.25);
      border-radius: 999px;
      background: rgba(94, 225, 162, 0.07);
      color: #b7f8d7;
      font-size: 0.82rem;
      font-weight: 750;
    }

    .status-dot {
      width: 8px;
      height: 8px;
      border-radius: 50%;
      background: var(--success);
      box-shadow: 0 0 0 5px rgba(94, 225, 162, 0.10);
      animation: pulse 2.2s infinite;
    }

    @keyframes pulse {
      50% {
        box-shadow: 0 0 0 10px rgba(94, 225, 162, 0);
      }
    }

    .hero h1 {
      max-width: 820px;
      margin-bottom: 23px;
      font-size: clamp(3.4rem, 8.5vw, 7rem);
      line-height: 0.91;
      letter-spacing: -0.075em;
    }

    .hero-copy {
      max-width: 690px;
      color: var(--muted);
      font-size: clamp(1.05rem, 2vw, 1.25rem);
    }

    .hero-copy strong {
      color: var(--text);
    }

    .hero-actions {
      display: flex;
      flex-wrap: wrap;
      gap: 13px;
      margin-top: 32px;
    }

    .button {
      display: inline-flex;
      min-height: 52px;
      align-items: center;
      justify-content: center;
      gap: 9px;
      padding: 0 19px;
      border: 1px solid transparent;
      border-radius: 15px;
      font-size: 0.94rem;
      font-weight: 850;
      transition:
        transform 180ms ease,
        border-color 180ms ease,
        background 180ms ease;
    }

    .button:hover {
      transform: translateY(-2px);
    }

    .button-primary {
      background: linear-gradient(135deg, var(--primary), #6d5dfc 55%, #277fe8);
      box-shadow: 0 16px 38px rgba(101, 75, 240, 0.25);
    }

    .button-secondary {
      border-color: var(--line);
      background: rgba(255, 255, 255, 0.05);
    }

    .button-secondary:hover {
      border-color: rgba(255, 255, 255, 0.21);
      background: rgba(255, 255, 255, 0.08);
    }

    .microcopy {
      margin-top: 24px;
      color: #727c92;
      font-size: 0.84rem;
    }

    .visual-card {
      position: relative;
      max-width: 440px;
      margin-left: auto;
    }

    .visual-card::before {
      position: absolute;
      z-index: -1;
      inset: -12%;
      border-radius: 50%;
      background:
        radial-gradient(circle, rgba(139, 92, 246, 0.28), transparent 58%);
      filter: blur(12px);
      content: "";
    }

    .portrait {
      position: relative;
      overflow: hidden;
      min-height: 540px;
      border: 1px solid rgba(255, 255, 255, 0.13);
      border-radius: 34px;
      background:
        linear-gradient(180deg, transparent 48%, rgba(5, 6, 11, 0.91)),
        linear-gradient(145deg, rgba(139, 92, 246, 0.22), rgba(34, 211, 238, 0.10)),
        var(--card-solid);
      box-shadow: var(--shadow);
    }

    .portrait img {
      position: absolute;
      inset: 0;
      height: 100%;
      object-fit: cover;
    }

    .portrait-fallback {
      position: absolute;
      inset: 0;
      display: grid;
      place-items: center;
      padding: 40px;
      color: rgba(255, 255, 255, 0.48);
      text-align: center;
    }

    .portrait-fallback span {
      display: block;
      margin-top: 10px;
      color: rgba(255, 255, 255, 0.32);
      font-size: 0.82rem;
    }

    .portrait-caption {
      position: absolute;
      z-index: 2;
      right: 25px;
      bottom: 25px;
      left: 25px;
    }

    .portrait-caption small {
      color: #b4bdd0;
      font-size: 0.76rem;
      font-weight: 800;
      letter-spacing: 0.12em;
      text-transform: uppercase;
    }

    .portrait-caption h3 {
      margin-top: 5px;
      font-size: 1.7rem;
      letter-spacing: -0.04em;
    }

    .floating-chip {
      position: absolute;
      z-index: 4;
      display: inline-flex;
      align-items: center;
      gap: 9px;
      padding: 11px 14px;
      border: 1px solid rgba(255, 255, 255, 0.14);
      border-radius: 15px;
      background: rgba(13, 16, 25, 0.82);
      box-shadow: 0 14px 36px rgba(0, 0, 0, 0.28);
      backdrop-filter: blur(14px);
      font-size: 0.81rem;
      font-weight: 800;
    }

    .chip-one {
      top: 54px;
      left: -42px;
    }

    .chip-two {
      right: -34px;
      bottom: 110px;
    }

    /* Metrics */
    .metrics {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      overflow: hidden;
      border: 1px solid var(--line);
      border-radius: var(--radius-lg);
      background: rgba(255, 255, 255, 0.035);
    }

    .metric {
      min-height: 150px;
      padding: 29px;
      border-right: 1px solid var(--line);
    }

    .metric:last-child {
      border-right: 0;
    }

    .metric strong {
      display: block;
      margin-bottom: 8px;
      font-size: clamp(1.65rem, 3vw, 2.55rem);
      line-height: 1;
      letter-spacing: -0.055em;
    }

    .metric span {
      color: var(--muted);
      font-size: 0.89rem;
    }

    /* About */
    .about-grid {
      display: grid;
      grid-template-columns: 0.9fr 1.1fr;
      gap: 28px;
      align-items: stretch;
    }

    .about-photo,
    .about-copy {
      border: 1px solid var(--line);
      border-radius: var(--radius-lg);
      background: var(--card);
      box-shadow: 0 22px 60px rgba(0, 0, 0, 0.18);
    }

    .about-photo {
      position: relative;
      overflow: hidden;
      min-height: 520px;
      background:
        linear-gradient(180deg, transparent 45%, rgba(7, 8, 13, 0.92)),
        linear-gradient(135deg, rgba(34, 211, 238, 0.13), rgba(139, 92, 246, 0.16)),
        var(--card-solid);
    }

    .about-photo img {
      position: absolute;
      inset: 0;
      height: 100%;
      object-fit: cover;
    }

    .about-photo-label {
      position: absolute;
      right: 24px;
      bottom: 22px;
      left: 24px;
      color: #c9d0df;
      font-size: 0.88rem;
    }

    .about-copy {
      padding: clamp(29px, 5vw, 55px);
    }

    .about-copy h3 {
      margin-bottom: 20px;
      font-size: clamp(1.8rem, 3.6vw, 3rem);
      line-height: 1.08;
      letter-spacing: -0.05em;
    }

    .about-copy > p {
      margin-bottom: 28px;
      color: var(--muted);
    }

    .principles {
      display: grid;
      gap: 12px;
    }

    .principle {
      display: grid;
      grid-template-columns: 42px 1fr;
      gap: 14px;
      align-items: start;
      padding: 17px;
      border: 1px solid var(--line);
      border-radius: var(--radius-sm);
      background: rgba(255, 255, 255, 0.025);
    }

    .principle-number {
      display: grid;
      width: 42px;
      height: 42px;
      place-items: center;
      border-radius: 12px;
      background: rgba(139, 92, 246, 0.12);
      color: #c8baff;
      font-size: 0.8rem;
      font-weight: 900;
    }

    .principle h4 {
      margin-bottom: 3px;
      font-size: 0.97rem;
    }

    .principle p {
      color: var(--muted);
      font-size: 0.86rem;
    }

    /* Projects */
    .projects-grid {
      display: grid;
      gap: 22px;
    }

    .project {
      display: grid;
      grid-template-columns: 1.04fr 0.96fr;
      overflow: hidden;
      min-height: 430px;
      border: 1px solid var(--line);
      border-radius: var(--radius-lg);
      background: var(--card);
      box-shadow: 0 22px 70px rgba(0, 0, 0, 0.18);
      transition:
        transform 220ms ease,
        border-color 220ms ease;
    }

    .project:hover {
      border-color: rgba(255, 255, 255, 0.21);
      transform: translateY(-4px);
    }

    .project:nth-child(even) .project-media {
      order: 2;
    }

    .project-media {
      position: relative;
      overflow: hidden;
      min-height: 360px;
      background:
        linear-gradient(145deg, rgba(139, 92, 246, 0.19), rgba(34, 211, 238, 0.08)),
        var(--card-solid);
    }

    .project-media img {
      position: absolute;
      inset: 0;
      height: 100%;
      object-fit: cover;
      transition: transform 500ms ease;
    }

    .project:hover .project-media img {
      transform: scale(1.025);
    }

    .media-placeholder {
      position: absolute;
      inset: 0;
      display: grid;
      place-items: center;
      padding: 30px;
      color: rgba(255, 255, 255, 0.42);
      text-align: center;
    }

    .media-placeholder code {
      display: block;
      margin-top: 8px;
      color: rgba(255, 255, 255, 0.28);
      font-size: 0.77rem;
    }

    .project-content {
      display: flex;
      flex-direction: column;
      justify-content: center;
      padding: clamp(30px, 6vw, 62px);
    }

    .project-label {
      margin-bottom: 15px;
      color: var(--secondary);
      font-size: 0.75rem;
      font-weight: 900;
      letter-spacing: 0.13em;
      text-transform: uppercase;
    }

    .project h3 {
      margin-bottom: 15px;
      font-size: clamp(1.7rem, 4vw, 2.75rem);
      line-height: 1.05;
      letter-spacing: -0.05em;
    }

    .project p {
      color: var(--muted);
    }

    .tags {
      display: flex;
      flex-wrap: wrap;
      gap: 8px;
      margin-top: 23px;
    }

    .tag {
      padding: 7px 10px;
      border: 1px solid var(--line);
      border-radius: 999px;
      background: rgba(255, 255, 255, 0.035);
      color: #c8cfde;
      font-size: 0.76rem;
      font-weight: 750;
    }

    .project-link {
      display: inline-flex;
      align-items: center;
      align-self: flex-start;
      gap: 9px;
      margin-top: 28px;
      color: #ffffff;
      font-size: 0.9rem;
      font-weight: 850;
    }

    .project-link span {
      transition: transform 180ms ease;
    }

    .project-link:hover span {
      transform: translateX(4px);
    }

    /* Stack */
    .stack-grid {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      gap: 14px;
    }

    .stack-card {
      min-height: 178px;
      padding: 23px;
      border: 1px solid var(--line);
      border-radius: var(--radius-md);
      background: rgba(255, 255, 255, 0.032);
      transition:
        transform 180ms ease,
        background 180ms ease;
    }

    .stack-card:hover {
      background: rgba(255, 255, 255, 0.055);
      transform: translateY(-3px);
    }

    .stack-icon {
      display: grid;
      width: 45px;
      height: 45px;
      margin-bottom: 25px;
      place-items: center;
      border: 1px solid rgba(255, 255, 255, 0.10);
      border-radius: 13px;
      background: linear-gradient(145deg, rgba(139, 92, 246, 0.14), rgba(34, 211, 238, 0.09));
      font-weight: 950;
    }

    .stack-card h3 {
      margin-bottom: 6px;
      font-size: 1rem;
    }

    .stack-card p {
      color: var(--muted);
      font-size: 0.83rem;
    }

    /* Timeline */
    .timeline {
      position: relative;
      display: grid;
      gap: 17px;
      max-width: 860px;
    }

    .timeline::before {
      position: absolute;
      top: 19px;
      bottom: 19px;
      left: 19px;
      width: 1px;
      background: linear-gradient(var(--primary), var(--secondary), transparent);
      content: "";
    }

    .timeline-item {
      position: relative;
      display: grid;
      grid-template-columns: 40px 1fr;
      gap: 20px;
      align-items: start;
    }

    .timeline-dot {
      z-index: 1;
      width: 39px;
      height: 39px;
      border: 9px solid var(--bg);
      border-radius: 50%;
      background: var(--primary);
      box-shadow: 0 0 0 1px rgba(255, 255, 255, 0.10);
    }

    .timeline-content {
      padding: 4px 0 29px;
    }

    .timeline-content small {
      color: var(--secondary);
      font-size: 0.74rem;
      font-weight: 900;
      letter-spacing: 0.12em;
      text-transform: uppercase;
    }

    .timeline-content h3 {
      margin: 5px 0 7px;
      font-size: 1.18rem;
    }

    .timeline-content p {
      color: var(--muted);
      font-size: 0.91rem;
    }

    /* Contact */
    .contact-card {
      position: relative;
      overflow: hidden;
      padding: clamp(36px, 7vw, 80px);
      border: 1px solid rgba(255, 255, 255, 0.14);
      border-radius: 34px;
      background:
        radial-gradient(circle at 85% 20%, rgba(34, 211, 238, 0.17), transparent 26rem),
        radial-gradient(circle at 5% 100%, rgba(139, 92, 246, 0.25), transparent 31rem),
        var(--card-solid);
      box-shadow: var(--shadow);
    }

    .contact-card::after {
      position: absolute;
      right: -50px;
      bottom: -70px;
      width: 270px;
      height: 270px;
      border: 1px solid rgba(255, 255, 255, 0.08);
      border-radius: 50%;
      content: "";
    }

    .contact-card h2 {
      max-width: 800px;
      margin-bottom: 18px;
      font-size: clamp(2.2rem, 6vw, 4.8rem);
      line-height: 0.98;
      letter-spacing: -0.065em;
    }

    .contact-card p {
      max-width: 670px;
      color: #aeb7ca;
    }

    .contact-actions {
      display: flex;
      flex-wrap: wrap;
      gap: 12px;
      margin-top: 30px;
    }

    footer {
      padding: 34px 0 50px;
      color: #747e93;
      font-size: 0.82rem;
    }

    .footer-grid {
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 24px;
    }

    .socials {
      display: flex;
      gap: 17px;
    }

    .socials a:hover {
      color: var(--text);
    }

    /* Reveal animation */
    .reveal {
      opacity: 0;
      transform: translateY(24px);
      transition:
        opacity 700ms ease,
        transform 700ms ease;
    }

    .reveal.visible {
      opacity: 1;
      transform: translateY(0);
    }

    /* Responsive */
    @media (max-width: 980px) {
      .hero-grid,
      .about-grid,
      .project {
        grid-template-columns: 1fr;
      }

      .visual-card {
        width: min(100%, 480px);
        margin: 10px auto 0;
      }

      .project:nth-child(even) .project-media {
        order: initial;
      }

      .metrics,
      .stack-grid {
        grid-template-columns: repeat(2, 1fr);
      }

      .metric:nth-child(2) {
        border-right: 0;
      }

      .metric:nth-child(-n + 2) {
        border-bottom: 1px solid var(--line);
      }
    }

    @media (max-width: 760px) {
      .section {
        padding: 78px 0;
      }

      .nav-links {
        display: none;
      }

      .hero {
        padding-top: 125px;
      }

      .hero h1 {
        font-size: clamp(3.2rem, 17vw, 5.3rem);
      }

      .portrait {
        min-height: 480px;
      }

      .chip-one {
        top: 22px;
        left: 18px;
      }

      .chip-two {
        right: 18px;
        bottom: 95px;
      }

      .project-media {
        min-height: 290px;
      }

      .footer-grid {
        align-items: flex-start;
        flex-direction: column;
      }
    }

    @media (max-width: 540px) {
      .container {
        width: min(calc(100% - 24px), var(--max-width));
      }

      .nav-shell {
        width: calc(100% - 18px);
      }

      .nav {
        min-height: 58px;
        padding-left: 13px;
      }

      .brand-name {
        display: none;
      }

      .nav-cta {
        padding: 9px 12px;
      }

      .metrics,
      .stack-grid {
        grid-template-columns: 1fr;
      }

      .metric {
        border-right: 0;
        border-bottom: 1px solid var(--line);
      }

      .metric:last-child {
        border-bottom: 0;
      }

      .hero-actions,
      .contact-actions {
        flex-direction: column;
      }

      .button {
        width: 100%;
      }

      .portrait {
        min-height: 420px;
      }

      .about-photo {
        min-height: 390px;
      }
    }

    @media (prefers-reduced-motion: reduce) {
      html {
        scroll-behavior: auto;
      }

      *,
      *::before,
      *::after {
        animation-duration: 0.01ms !important;
        animation-iteration-count: 1 !important;
        transition-duration: 0.01ms !important;
      }

      .reveal {
        opacity: 1;
        transform: none;
      }
    }
  </style>
</head>

<body>
  <!--
    CAMBIOS RÁPIDOS ANTES DE PUBLICAR:
    1. Reemplaza TU_USUARIO por tu usuario real de GitHub.
    2. Cambia los enlaces de LinkedIn, correo y repositorios.
    3. Coloca tus imágenes dentro de /assets con estos nombres:
       - perfil.jpg
       - trabajando.jpg
       - camur.jpg
       - retail.jpg
       - machine-learning.jpg
    4. Puedes borrar cualquier proyecto que todavía no quieras mostrar.
  -->

  <header class="nav-shell">
    <nav class="nav" aria-label="Navegación principal">
      <a class="brand" href="#inicio">
        <span class="brand-mark">F</span>
        <span class="brand-name">Fabri.dev</span>
      </a>

      <ul class="nav-links">
        <li><a href="#sobre-mi">Sobre mí</a></li>
        <li><a href="#proyectos">Proyectos</a></li>
        <li><a href="#stack">Stack</a></li>
        <li><a href="#ruta">Ruta</a></li>
      </ul>

      <a
        class="nav-cta"
        href="https://github.com/TU_USUARIO"
        target="_blank"
        rel="noreferrer"
      >
        GitHub ↗
      </a>
    </nav>
  </header>

  <main>
    <section class="hero" id="inicio">
      <div class="container hero-grid">
        <div>
          <div class="status">
            <span class="status-dot"></span>
            Construyendo proyectos y mejorando cada commit
          </div>

          <h1>
            Hola, soy
            <span class="gradient-text">Fabri.</span>
          </h1>

          <p class="hero-copy">
            Estudiante de <strong>Ingeniería de Sistemas</strong> enfocado en
            automatización, productos digitales e
            <strong>IA aplicada a negocios reales</strong>. Me interesa conectar
            tecnología, estrategia y ejecución para convertir problemas cotidianos
            en soluciones útiles.
          </p>

          <div class="hero-actions">
            <a class="button button-primary" href="#proyectos">
              Ver proyectos <span aria-hidden="true">↓</span>
            </a>
            <a
              class="button button-secondary"
              href="https://github.com/TU_USUARIO"
              target="_blank"
              rel="noreferrer"
            >
              Explorar GitHub <span aria-hidden="true">↗</span>
            </a>
          </div>

          <p class="microcopy">
            Ingeniería de Sistemas · Automatización · IA · Negocios · Perú
          </p>
        </div>

        <div class="visual-card reveal">
          <div class="floating-chip chip-one">⚙️ Automatización</div>
          <div class="floating-chip chip-two">🧠 IA + Negocios</div>

          <figure class="portrait">
            <div class="portrait-fallback">
              <div>
                Espacio para tu fotografía principal
                <span>assets/perfil.jpg</span>
              </div>
            </div>

            <!-- Tu fotografía principal -->
            <img
              src="assets/perfil.jpg"
              alt="Fotografía de Fabri"
              onerror="this.style.display='none'"
            />

            <figcaption class="portrait-caption">
              <small>Perfil</small>
              <h3>Constructor de soluciones digitales</h3>
            </figcaption>
          </figure>
        </div>
      </div>
    </section>

    <section class="container" aria-label="Resumen profesional">
      <div class="metrics reveal">
        <article class="metric">
          <strong>+3</strong>
          <span>Proyectos entre automatización, web y Machine Learning</span>
        </article>
        <article class="metric">
          <strong>7.º</strong>
          <span>Ciclo de Ingeniería de Sistemas en la Universidad de Lima</span>
        </article>
        <article class="metric">
          <strong>360°</strong>
          <span>Visión técnica, comercial y estratégica del producto</span>
        </article>
        <article class="metric">
          <strong>1 meta</strong>
          <span>Crear tecnología que genere valor medible para empresas</span>
        </article>
      </div>
    </section>

    <section class="section" id="sobre-mi">
      <div class="container">
        <div class="section-heading reveal">
          <span class="eyebrow">Sobre mí</span>
          <h2>
            No me interesa usar tecnología
            <span class="gradient-text">solo porque está de moda.</span>
          </h2>
          <p>
            Busco comprender el problema, diseñar una solución clara y hacer que
            realmente funcione para las personas que la utilizarán.
          </p>
        </div>

        <div class="about-grid">
          <figure class="about-photo reveal">
            <div class="portrait-fallback">
              <div>
                Espacio para una foto trabajando, estudiando o presentando
                <span>assets/trabajando.jpg</span>
              </div>
            </div>

            <img
              src="assets/trabajando.jpg"
              alt="Fabri trabajando en un proyecto"
              onerror="this.style.display='none'"
            />

            <figcaption class="about-photo-label">
              Construir, probar, aprender y volver a mejorar.
            </figcaption>
          </figure>

          <article class="about-copy reveal">
            <h3>Tecnología con criterio de negocio.</h3>

            <p>
              Actualmente estudio Ingeniería de Sistemas y desarrollo proyectos
              donde combino automatización, interfaces web, análisis de datos y
              herramientas de inteligencia artificial. Mi objetivo profesional es
              liderar productos y equipos capaces de solucionar problemas concretos
              en pequeñas y medianas empresas.
            </p>

            <div class="principles">
              <div class="principle">
                <span class="principle-number">01</span>
                <div>
                  <h4>Primero entender el problema</h4>
                  <p>
                    Antes de elegir una herramienta, investigo el proceso, el usuario
                    y el resultado que realmente importa.
                  </p>
                </div>
              </div>

              <div class="principle">
                <span class="principle-number">02</span>
                <div>
                  <h4>Construir soluciones simples</h4>
                  <p>
                    Una solución valiosa debe ser clara, mantenible y fácil de usar,
                    no solamente técnicamente interesante.
                  </p>
                </div>
              </div>

              <div class="principle">
                <span class="principle-number">03</span>
                <div>
                  <h4>Pensar en crecimiento</h4>
                  <p>
                    Me interesa que cada proyecto pueda validarse, mejorar con datos
                    y convertirse en un producto escalable.
                  </p>
                </div>
              </div>
            </div>
          </article>
        </div>
      </div>
    </section>

    <section class="section" id="proyectos">
      <div class="container">
        <div class="section-heading reveal">
          <span class="eyebrow">Proyectos seleccionados</span>
          <h2>
            Aprendo construyendo
            <span class="gradient-text">soluciones reales.</span>
          </h2>
          <p>
            Estos proyectos representan mi interés por la automatización, la
            adquisición de clientes, los sistemas empresariales y el análisis de
            datos.
          </p>
        </div>

        <div class="projects-grid">
          <!-- PROYECTO 1 -->
          <article class="project reveal">
            <div class="project-media">
              <div class="media-placeholder">
                <div>
                  Imagen de la landing, chatbot o panel de CAMUR
                  <code>assets/camur.jpg</code>
                </div>
              </div>
              <img
                src="assets/camur.jpg"
                alt="Proyecto digital CAMUR"
                onerror="this.style.display='none'"
              />
            </div>

            <div class="project-content">
              <span class="project-label">Automatización + Growth</span>
              <h3>CAMUR: captación digital para implantología</h3>
              <p>
                Diseño de un sistema para captar y filtrar pacientes de alto valor:
                landing page, campañas de búsqueda, conversación por WhatsApp,
                clasificación de leads y registro de información para facilitar el
                seguimiento comercial.
              </p>

              <div class="tags">
                <span class="tag">Cloudflare Pages</span>
                <span class="tag">GitHub</span>
                <span class="tag">n8n</span>
                <span class="tag">WhatsApp API</span>
                <span class="tag">Google Sheets</span>
              </div>

              <!-- Reemplaza # por el enlace al repositorio o demo -->
              <a class="project-link" href="#">
                Ver caso del proyecto <span>→</span>
              </a>
            </div>
          </article>

          <!-- PROYECTO 2 -->
          <article class="project reveal">
            <div class="project-media">
              <div class="media-placeholder">
                <div>
                  Imagen del flujo, inventario, tienda o prototipo
                  <code>assets/retail.jpg</code>
                </div>
              </div>
              <img
                src="assets/retail.jpg"
                alt="Sistema para negocios de calzado"
                onerror="this.style.display='none'"
              />
            </div>

            <div class="project-content">
              <span class="project-label">Producto para pymes</span>
              <h3>Automatización comercial para tiendas de calzado</h3>
              <p>
                Investigación y diseño de una solución orientada a negocios que
                controlan inventario, tallas, ventas y consultas por WhatsApp. La
                propuesta busca reducir errores operativos y convertir conversaciones
                repetitivas en procesos organizados.
              </p>

              <div class="tags">
                <span class="tag">User Research</span>
                <span class="tag">AppSheet</span>
                <span class="tag">Automatización</span>
                <span class="tag">Inventario</span>
                <span class="tag">WhatsApp</span>
              </div>

              <a class="project-link" href="#">
                Ver investigación <span>→</span>
              </a>
            </div>
          </article>

          <!-- PROYECTO 3 -->
          <article class="project reveal">
            <div class="project-media">
              <div class="media-placeholder">
                <div>
                  Imagen de métricas, matriz de confusión o notebook
                  <code>assets/machine-learning.jpg</code>
                </div>
              </div>
              <img
                src="assets/machine-learning.jpg"
                alt="Proyecto de Machine Learning sobre cáncer cervical"
                onerror="this.style.display='none'"
              />
            </div>

            <div class="project-content">
              <span class="project-label">Machine Learning</span>
              <h3>Clasificación de riesgo de cáncer cervical</h3>
              <p>
                Proyecto académico de clasificación con datos desbalanceados. Incluyó
                limpieza, tratamiento de valores faltantes, prevención de fuga de
                información, SMOTE y comparación de modelos priorizando el recall de
                la clase minoritaria.
              </p>

              <div class="tags">
                <span class="tag">Python</span>
                <span class="tag">Pandas</span>
                <span class="tag">Scikit-learn</span>
                <span class="tag">SMOTE</span>
                <span class="tag">SVM</span>
              </div>

              <a class="project-link" href="#">
                Ver repositorio <span>→</span>
              </a>
            </div>
          </article>
        </div>
      </div>
    </section>

    <section class="section" id="stack">
      <div class="container">
        <div class="section-heading reveal">
          <span class="eyebrow">Herramientas</span>
          <h2>
            Mi stack está en
            <span class="gradient-text">constante evolución.</span>
          </h2>
          <p>
            No presento esta lista como dominio absoluto, sino como las tecnologías
            con las que estudio, experimento y construyo.
          </p>
        </div>

        <div class="stack-grid">
          <article class="stack-card reveal">
            <div class="stack-icon">&lt;/&gt;</div>
            <h3>Web</h3>
            <p>HTML, CSS, JavaScript, Framer y despliegues en Cloudflare Pages.</p>
          </article>

          <article class="stack-card reveal">
            <div class="stack-icon">Git</div>
            <h3>Versionamiento</h3>
            <p>Git, GitHub, repositorios, ramas, commits y publicación de proyectos.</p>
          </article>

          <article class="stack-card reveal">
            <div class="stack-icon">⚙</div>
            <h3>Automatización</h3>
            <p>n8n, webhooks, Google Sheets, APIs y flujos conversacionales.</p>
          </article>

          <article class="stack-card reveal">
            <div class="stack-icon">WA</div>
            <h3>Conversaciones</h3>
            <p>WhatsApp Cloud API, WATI y diseño de embudos de calificación.</p>
          </article>

          <article class="stack-card reveal">
            <div class="stack-icon">Py</div>
            <h3>Datos</h3>
            <p>Python, Pandas, análisis exploratorio y preparación de datasets.</p>
          </article>

          <article class="stack-card reveal">
            <div class="stack-icon">ML</div>
            <h3>Machine Learning</h3>
            <p>Scikit-learn, clasificación, validación y evaluación de modelos.</p>
          </article>

          <article class="stack-card reveal">
            <div class="stack-icon">UX</div>
            <h3>Producto</h3>
            <p>Investigación de usuarios, levantamiento de dolores y prototipado.</p>
          </article>

          <article class="stack-card reveal">
            <div class="stack-icon">↗</div>
            <h3>Negocio</h3>
            <p>Validación, adquisición de clientes, propuesta de valor y estrategia.</p>
          </article>
        </div>
      </div>
    </section>

    <section class="section" id="ruta">
      <div class="container">
        <div class="section-heading reveal">
          <span class="eyebrow">Mi ruta</span>
          <h2>
            Una carrera construida
            <span class="gradient-text">proyecto por proyecto.</span>
          </h2>
        </div>

        <div class="timeline">
          <article class="timeline-item reveal">
            <span class="timeline-dot"></span>
            <div class="timeline-content">
              <small>Ahora</small>
              <h3>Fortalecer Git, GitHub y mi portafolio</h3>
              <p>
                Documentar mejor mis avances, publicar proyectos completos y mostrar
                con claridad qué problema resolví en cada uno.
              </p>
            </div>
          </article>

          <article class="timeline-item reveal">
            <span class="timeline-dot"></span>
            <div class="timeline-content">
              <small>Siguiente etapa</small>
              <h3>Validar soluciones con empresas reales</h3>
              <p>
                Pasar de prototipos y pilotos a sistemas utilizados por negocios,
                midiendo ahorro de tiempo, calidad operativa y resultados comerciales.
              </p>
            </div>
          </article>

          <article class="timeline-item reveal">
            <span class="timeline-dot"></span>
            <div class="timeline-content">
              <small>Visión</small>
              <h3>Crear una empresa tecnológica propia</h3>
              <p>
                Liderar una compañía que desarrolle automatizaciones, productos de
                software e inteligencia artificial aplicada para pymes.
              </p>
            </div>
          </article>
        </div>
      </div>
    </section>

    <section class="section" id="contacto">
      <div class="container">
        <div class="contact-card reveal">
          <span class="eyebrow">Contacto</span>
          <h2>¿Construimos algo que tenga impacto real?</h2>
          <p>
            Estoy abierto a proyectos, colaboraciones y oportunidades donde pueda
            aprender, aportar visión de producto y convertir una necesidad empresarial
            en una solución tecnológica.
          </p>

          <div class="contact-actions">
            <!-- Reemplaza el correo -->
            <a class="button button-primary" href="mailto:TU_CORREO">
              Escribirme por correo ↗
            </a>

            <!-- Reemplaza el enlace -->
            <a
              class="button button-secondary"
              href="https://www.linkedin.com/in/TU_LINKEDIN/"
              target="_blank"
              rel="noreferrer"
            >
              LinkedIn ↗
            </a>
          </div>
        </div>
      </div>
    </section>
  </main>

  <footer>
    <div class="container footer-grid">
      <p>
        © <span id="year"></span> Fabri. Construido con HTML, CSS y curiosidad.
      </p>

      <div class="socials">
        <a
          href="https://github.com/TU_USUARIO"
          target="_blank"
          rel="noreferrer"
        >GitHub</a>
        <a
          href="https://www.linkedin.com/in/TU_LINKEDIN/"
          target="_blank"
          rel="noreferrer"
        >LinkedIn</a>
        <a href="mailto:TU_CORREO">Correo</a>
      </div>
    </div>
  </footer>

  <script>
    document.getElementById("year").textContent = new Date().getFullYear();

    const revealElements = document.querySelectorAll(".reveal");

    if ("IntersectionObserver" in window) {
      const observer = new IntersectionObserver(
        (entries) => {
          entries.forEach((entry) => {
            if (entry.isIntersecting) {
              entry.target.classList.add("visible");
              observer.unobserve(entry.target);
            }
          });
        },
        { threshold: 0.12 }
      );

      revealElements.forEach((element) => observer.observe(element));
    } else {
      revealElements.forEach((element) => element.classList.add("visible"));
    }
  </script>
</body>
</html>
'''

readme = '''PORTAFOLIO DE FABRI — INSTRUCCIONES

1. Abre index.html y reemplaza:
   - TU_USUARIO
   - TU_LINKEDIN
   - TU_CORREO
   - Los enlaces href="#" de cada proyecto

2. Guarda tus imágenes en la carpeta assets:
   - perfil.jpg
   - trabajando.jpg
   - camur.jpg
   - retail.jpg
   - machine-learning.jpg

3. Para publicar con GitHub Pages:
   - Sube index.html y la carpeta assets al repositorio.
   - Ve a Settings > Pages.
   - En Build and deployment elige Deploy from a branch.
   - Selecciona main y /root.
   - Guarda y abre el enlace publicado.

4. Recomendación:
   - Usa capturas reales de tus proyectos.
   - No coloques información personal sensible.
   - Añade un README.md al repositorio explicando el objetivo y las tecnologías.
'''

(base / "index.html").write_text(html, encoding="utf-8")
(assets / "COLOCA_AQUI_TUS_IMAGENES.txt").write_text(
    "perfil.jpg\ntrabajando.jpg\ncamur.jpg\nretail.jpg\nmachine-learning.jpg\n",
    encoding="utf-8",
)
(base / "INSTRUCCIONES.txt").write_text(readme, encoding="utf-8")

zip_path = Path("/mnt/data/fabri-github-portfolio.zip")
with zipfile.ZipFile(zip_path, "w", zipfile.ZIP_DEFLATED) as zf:
    for file in base.rglob("*"):
        if file.is_file():
            zf.write(file, file.relative_to(base.parent))

print(f"Creado: {base / 'index.html'}")
print(f"Creado: {zip_path}")
