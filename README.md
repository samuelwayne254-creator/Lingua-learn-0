# Lingua-learn-0
Free learning 
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>LinguaVerse • Learn Languages in 3D</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r134/three.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600&amp;family=Space+Grotesk:wght@500;600&amp;display=swap');
        
        :root {
            --primary: #00ff9d;
        }

        * {
            transition-property: color, background-color, border-color, text-decoration-color, fill, stroke, transform;
            transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);
            transition-duration: 200ms;
        }

        .hero-canvas {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 1;
        }

        .glass {
            background: rgba(255, 255, 255, 0.08);
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
        }

        .card-3d {
            transition: all 0.4s cubic-bezier(0.34, 1.56, 0.64, 1);
        }

        .card-3d:hover {
            transform: perspective(1000px) rotateX(8deg) rotateY(12deg) scale(1.04);
            box-shadow: 0 25px 50px -12px rgb(0 255 157 / 0.25);
        }

        .nav-link {
            position: relative;
        }
        
        .nav-link:after {
            content: '';
            position: absolute;
            width: 0;
            height: 2px;
            bottom: -2px;
            left: 0;
            background-color: var(--primary);
        }
        
        .nav-link:hover:after {
            width: 100%;
        }

        .section-header {
            font-family: 'Space Grotesk', sans-serif;
        }

        /* Mobile-specific improvements */
        @media (max-width: 640px) {
            .hero-text {
                font-size: 2.75rem !important;
                line-height: 1.05 !important;
            }
            
            .hero-subtitle {
                font-size: 1.25rem !important;
            }
        }

        /* Smooth scrolling */
        html {
            scroll-behavior: smooth;
        }

        /* Better touch targets */
        .touch-target {
            min-height: 48px;
            min-width: 48px;
        }
    </style>
</
