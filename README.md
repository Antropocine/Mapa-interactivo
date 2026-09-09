<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mapa Histórico Interactivo de las Relaciones Internacionales</title>
    
    <!-- Librerías de diseño del mapa (Leaflet) -->
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <style>
        /* Estilos generales para PC y base */
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            margin: 0;
            padding: 15px;
            background-color: #f0f4f8;
            color: #333;
        }
        h1 {
            font-size: 1.8em;
            text-align: center;
            color: #2c3e50;
            margin-top: 10px;
        }
        h2 {
            font-size: 1.4em;
            color: #2c3e50;
        }
        p.intro {
            text-align: center;
            color: #555;
            margin-bottom: 20px;
            font-size: 1em;
            padding: 0 10px;
        }
        
        /* Contenedor del Mapa adaptable */
        #mi-mapa {
            height: 500px;
            width: 100%;
            max-width: 1100px;
            margin: 0 auto 30px auto;
            border: 3px solid #bdc3c7;
            border-radius: 10px;
            box-shadow: 0 6px 12px rgba(0,0,0,0.15);
        }

        /* Estilos de los marcadores numerados */
        .marcador-numero {
            background-color: #e74c3c;
            color: white;
            border-radius: 50%;
            width: 30px;
            height: 30px;
            display: flex;
            justify-content: center;
            align-items: center;
            font-weight: bold;
            border: 2px solid white;
            box-shadow: 0 3px 6px rgba(0,0,0,0.5);
            font-size: 14px;
        }

        /* Contenido emergente del mapa */
        .popup-fecha {
            color: #e67e22;
            font-weight: bold;
            font-size: 1em;
            margin-bottom: 6px;
            border-bottom: 1px solid #eee;
            padding-bottom: 4px;
        }
        .popup-desc {
            font-size: 0.9em;
            line-height: 1.3;
            color: #444;
        }

        /* Contenedor de la Trivia adaptable */
        #trivia-container {
            max-width: 900px;
            margin: 0 auto;
            background: white;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 6px 12px rgba(0,0,0,0.1);
        }
        .pregunta-bloque {
            margin-bottom: 20px;
            padding-bottom: 12px;
            border-bottom: 1px solid #ecf0f1;
        }
        .pregunta {
            font-weight: bold;
            margin-bottom: 10px;
            color: #2980b9;
            font-size: 1em;
        }
        .opciones label {
            display: block;
            margin-bottom: 8px;
            cursor: pointer;
            padding: 10px;
            background-color: #f9f9f9;
            border-radius: 5px;
            font-size: 0.95em;
            transition: background 0.3s;
        }
        .opciones label:hover {
            background-color: #eaf2f8;
        }
        button {
            display: block;
            width: 100%;
            max-width: 300px;
            margin: 25px auto 0 auto;
            padding: 12px;
            font-size: 16px;
            background-color: #27ae60;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-weight: bold;
            transition: background 0.3s;
        }
        button:hover {
            background-color: #2ecc71;
        }
        #resultado-trivia {
            text-align: center;
            font-weight: bold;
            margin-top: 15px;
            font-size: 1.1em;
        }

        /* DISEÑO ADAPTABLE PARA CELULARES (Pantallas menores a 768px) */
        @media screen and (max-width: 768px) {
            body {
                padding: 5px;
            }
            h1 {
                font-size: 1.4em;
            }
            #mi-mapa {
                height: 380px;
                border-radius: 5px;
            }
            #trivia-container {
                padding: 12px;
                border-radius: 5px;
            }
            .opciones label {
                padding: 12px;
                font-size: 0.9em;
            }
        }
    </style>
</head>
<body>

    <h1>Evolución del Mapa Internacional</h1>
    <p class="intro">Realizado por Leison Chia Y Camila Reyes.</p>
    <p class="intro">Sigue los números en el mapa para explorar cronológicamente cómo la historia ha redibujado las fronteras.</p>

    <!-- MAPA -->
    <div id="mi-mapa"></div>

    <!-- TRIVIA -->
    <div id="trivia-container">
        <h2>Trivia Histórica (10 Preguntas)</h2>
        <p style="color:#7f8c8d; margin-bottom: 15px; font-size: 0.9em;">Pon a prueba tus conocimientos sobre la lectura.</p>
        <div id="quiz"></div>
        <button onclick="calificarTrivia()">Calificar respuestas</button>
        <div id="resultado-trivia"></div>
    </div>

    <!-- Scripts de Leaflet -->
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <script>
        // Inicializar Mapa
        var mapa = L.map('mi-mapa').setView([25.0, -10.0], 2);

        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            maxZoom: 19,
            attribution: '© OpenStreetMap'
        }).addTo(mapa);

        // Base de Datos de los Eventos (Cronológicos)
        var eventos = [
            {
                lat: 40.4168, lng: -3.7038, 
                fecha: "1492",
                titulo: "La globalización del mundo", 
                descripcion: "Año de la 'reconquista' de la Península ibérica y la formación del primer Estado-nación. Marca el inicio de la época colonial en América e inserta al continente a la dinámica del sistema europeo. Tras la conquista, España se convierte en la primera gran potencia del emergente sistema de Estados nacionales[cite: 1]."
            },
            {
                lat: 51.9606, lng: 7.6261, 
                fecha: "1648",
                titulo: "La Paz de Westfalia", 
                descripcion: "Termina con la Guerra de los Treinta Años y sienta las bases para el surgimiento de los conceptos de soberanía y Estado soberano. La 'razón de Estado' se vuelve el principio rector de la diplomacia europea, emergiendo Francia, Austria y las Provincias Unidas (Holanda) como nuevas potencias[cite: 1]."
            },
            {
                lat: 51.5074, lng: -0.1278, 
                fecha: "1756 - 1763",
                titulo: "La guerra de los Siete años", 
                descripcion: "Conflicto dirigido por los intereses mercantilistas británicos para destruir a Francia. Con la Paz de París, Gran Bretaña se consolida como la potencia imperial y comercial más importante del planeta al adquirir Canadá y expandir sus mercados hacia Oriente en la India y las islas del Caribe[cite: 1]."
            },
            {
                lat: 4.5709, lng: -74.2973, 
                fecha: "1776 - 1838",
                titulo: "La independencia de las Américas", 
                descripcion: "El proceso de descolonización marca un parteaguas. Comenzó con las Trece colonias en 1776 y las posesiones españolas en 1810. Para 1838, existían 18 Estados independientes en el continente. Ante esto, Europa deja de ser el único actor del sistema internacional[cite: 1]."
            },
            {
                lat: 48.2082, lng: 16.3738, 
                fecha: "1815",
                titulo: "El Congreso de Viena", 
                descripcion: "Tras derrotar a la Francia napoleónica, los artífices del Acta final buscaron el equilibrio del poder en Europa. Se evitó la humillación de Francia y el continente experimentó el 'concierto europeo', un periodo de relativa paz de casi 40 años sin guerras entre las grandes potencias[cite: 1]."
            },
            {
                lat: 48.8566, lng: 2.3522, 
                fecha: "1919",
                titulo: "El Tratado de París", 
                descripcion: "Recomposición trascendental tras la Primera Guerra Mundial. Aplicó selectivamente el principio de la 'autodeterminación nacional' para reconstruir fronteras. Marcó el colapso de los imperios Austro-Húngaro y Otomano, y Gran Bretaña cedió el liderazgo económico a Estados Unidos[cite: 1]."
            },
            {
                lat: 44.2597, lng: -71.5034, 
                fecha: "1944",
                titulo: "Acuerdos de Bretton Woods", 
                descripcion: "Reunión de 44 representantes para crear un nuevo orden monetario internacional capaz de prevenir un colapso económico. De aquí surgieron el Fondo Monetario Internacional y el Banco Mundial, para otorgar préstamos y promover el desarrollo[cite: 1]."
            },
            {
                lat: 55.7558, lng: 37.6173, 
                fecha: "1945 - 1989",
                titulo: "La Guerra Fría y la Descolonización", 
                descripcion: "Emergen Estados Unidos y la Unión Soviética como superpotencias. El mundo se dividió en tres grandes bloques (occidentales, comunistas y no-alineados). Al mismo tiempo, más de 60 nuevos países nacieron en Asia y África, enfrentando guerras internas por fronteras artificiales[cite: 1]."
            },
            {
                lat: 52.5162, lng: 13.3777, 
                fecha: "9 de noviembre de 1989",
                titulo: "La Caída del Muro de Berlín", 
                descripcion: "El fin de la confrontación ideológica bipolar. Contrario a quienes predecían 'el fin de la historia', el colapso soviético evidenció que emergían dos manifestaciones antagónicas de la modernidad para redibujar el mapa: la integración y la fragmentación[cite: 1]."
            },
            {
                lat: 50.8503, lng: 4.3517, 
                fecha: "Actualidad",
                titulo: "Integración vs. Fragmentación", 
                descripcion: "Europa es el claro ejemplo: por un lado, una fuerte integración económica (Unión Europea); por el otro, cruentas fragmentaciones nacionalistas (Croacia, Bosnia, Kosovo). El mundo sigue transformándose debido a fuerzas políticas, económicas, religiosas y culturales complejas[cite: 1]."
            }
        ];

        // Crear marcadores numerados
        eventos.forEach(function(evento, index) {
            var numero = index + 1;
            
            var iconoNumerado = L.divIcon({
                className: 'custom-div-icon',
                html: "<div class='marcador-numero'>" + numero + "</div>",
                iconSize: [30, 30],
                iconAnchor: [15, 15]
            });

            var marcador = L.marker([evento.lat, evento.lng], {icon: iconoNumerado}).addTo(mapa);
            
            var contenidoPopup = `
                <div style="max-width: 220px;">
                    <div class="popup-fecha">⏳ ${evento.fecha}</div>
                    <h3 style="margin: 4px 0; color: #2c3e50; font-size: 1.1em;">${numero}. ${evento.titulo}</h3>
                    <p class="popup-desc">${evento.descripcion}</p>
                </div>
            `;
            
            marcador.bindPopup(contenidoPopup);
        });

        // Trivia
        const preguntasTrivia = [
            {
                pregunta: "1. Según el texto, ¿qué año marca la inserción de América a la dinámica política europea y consolida a España como gran potencia?[cite: 1]",
                opciones: ["1492", "1648", "1776"],
                respuestaCorrecta: 0
            },
            {
                pregunta: "2. ¿Qué tratado terminó con la Guerra de los Treinta Años y sentó las bases de la soberanía y el Estado soberano?[cite: 1]",
                opciones: ["El Tratado de París", "La Paz de Westfalia", "El Congreso de Viena"],
                respuestaCorrecta: 1
            },
            {
                pregunta: "3. Tras la Guerra de los Siete años (1756-1763), ¿qué país se consolidó como la potencia imperial y comercial más importante del planeta?[cite: 1]",
                opciones: ["Francia", "España", "Gran Bretaña"],
                respuestaCorrecta: 2
            },
            {
                pregunta: "4. ¿Qué suceso provocó que Europa dejara de ser el único actor del sistema internacional en los siglos XVIII y XIX?[cite: 1]",
                opciones: ["La Revolución Francesa", "La independencia de las Américas", "La creación de la ONU"],
                respuestaCorrecta: 1
            },
            {
                pregunta: "5. ¿Cuál fue el objetivo principal del Congreso de Viena en 1815?[cite: 1]",
                opciones: ["Establecer el libre comercio mundial", "Humillar a Francia tras las guerras", "Buscar el equilibrio del poder en Europa"],
                respuestaCorrecta: 2
            },
            {
                pregunta: "6. ¿Qué principio se aplicó selectivamente en el Tratado de París (1919) para reconstruir las fronteras tras la Primera Guerra Mundial?[cite: 1]",
                opciones: ["El equilibrio del poder", "La autodeterminación nacional", "La destrucción mutua asegurada"],
                respuestaCorrecta: 1
            },
            {
                pregunta: "7. ¿Qué instituciones nacieron en los acuerdos de Bretton Woods para prevenir colapsos económicos?[cite: 1]",
                opciones: ["La OTAN y el Pacto de Varsovia", "El Fondo Monetario Internacional y el Banco Mundial", "La Unión Europea y el Euro"],
                respuestaCorrecta: 1
            },
            {
                pregunta: "8. Durante la Guerra Fría, el mapa internacional se dividió principalmente en tres bloques: occidentales, comunistas y...[cite: 1]",
                opciones: ["Asiáticos", "No-alineados", "Imperialistas"],
                respuestaCorrecta: 1
            },
            {
                pregunta: "9. ¿Qué dos fuerzas antagónicas de la modernidad se hicieron más evidentes tras la caída del Muro de Berlín?[cite: 1]",
                opciones: ["Capitalismo y Comunismo", "Norte y Sur", "Integración y fragmentación"],
                respuestaCorrecta: 2
            },
            {
                pregunta: "10. Según los autores, ¿por qué es difícil describir el mapa político actual?[cite: 1]",
                opciones: ["Porque es el reflejo de tendencias complejas, contradictorias y de constante cambio", "Porque solo depende de los bloques comerciales", "Porque las fronteras ya no existen"],
                respuestaCorrecta: 0
            }
        ];

        const quizContainer = document.getElementById('quiz');

        preguntasTrivia.forEach((item, index) => {
            let opcionesHTML = '';
            item.opciones.forEach((opcion, i) => {
                opcionesHTML += `<label><input type="radio" name="pregunta${index}" value="${i}"> ${opcion}</label>`;
            });
            
            quizContainer.innerHTML += `
                <div class="pregunta-bloque">
                    <div class="pregunta">${item.pregunta}</div>
                    <div class="opciones">${opcionesHTML}</div>
                </div>
            `;
        });

        function calificarTrivia() {
            let puntaje = 0;
            preguntasTrivia.forEach((item, index) => {
                const opcionesSeleccionadas = document.querySelector(`input[name="pregunta${index}"]:checked`);
                if (opcionesSeleccionadas && parseInt(opcionesSeleccionadas.value) === item.respuestaCorrecta) {
                    puntaje++;
                }
            });

            const resultadoDiv = document.getElementById('resultado-trivia');
            if (puntaje === 10) {
                resultadoDiv.innerHTML = `¡Puntaje perfecto! ${puntaje} de 10. ¡Excelente trabajo! 🏆`;
                resultadoDiv.style.color = "#27ae60";
            } else if (puntaje >= 6) {
                resultadoDiv.innerHTML = `¡Buen trabajo! Obtuviste ${puntaje} de 10. 👍`;
                resultadoDiv.style.color = "#f39c12";
            } else {
                resultadoDiv.innerHTML = `Obtuviste ${puntaje} de 10. Repasa los puntos del mapa y prueba otra vez. 📚`;
                resultadoDiv.style.color = "#c0392b";
            }
        }
    </script>

</body>
</html>
