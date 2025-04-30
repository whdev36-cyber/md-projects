# HTML Comprehensive Project: Personal Portfolio Website

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="Professional portfolio of Jane Doe, Web Developer">
    <meta name="keywords" content="web development, portfolio, HTML, CSS, JavaScript">
    <title>Jane Doe | Web Developer</title>
    <base href="https://www.janedoeportfolio.com/">
    <link rel="stylesheet" href="styles.css">
    <link rel="icon" href="favicon.ico" type="image/x-icon">
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            line-height: 1.6;
            color: #333;
        }
        header {
            background-color: #2c3e50;
            color: white;
        }
    </style>
</head>
<body>
    <header>
        <h1>Jane Doe</h1>
        <nav>
            <ul>
                <li><a href="#about">About</a></li>
                <li><a href="#projects">Projects</a></li>
                <li><a href="#skills">Skills</a></li>
                <li><a href="#contact">Contact</a></li>
            </ul>
        </nav>
        <progress value="75" max="100"></progress>
        <meter value="0.7">70%</meter>
    </header>

    <main>
        <section id="about">
            <h2>About Me</h2>
            <article>
                <img src="profile.jpg" alt="Jane Doe portrait" width="200">
                <p>Hi, I'm <strong>Jane Doe</strong>, a passionate <em>web developer</em> with <mark>5 years of experience</mark> creating modern, responsive websites.</p>
                <blockquote cite="https://example.com">
                    "The web is what you make of it." <cite>- Anonymous</cite>
                </blockquote>
                <p>I specialize in <abbr title="HyperText Markup Language">HTML</abbr>, <abbr title="Cascading Style Sheets">CSS</abbr>, and JavaScript.</p>
                <details>
                    <summary>More about my background</summary>
                    <p>I graduated from <time datetime="2018-06">June 2018</time> with a degree in Computer Science.</p>
                </details>
            </article>
        </section>

        <section id="projects">
            <h2>Featured Projects</h2>
            <article>
                <h3>E-commerce Website</h3>
                <p>A fully responsive online store built with:</p>
                <ul>
                    <li>HTML5</li>
                    <li>CSS3</li>
                    <li>JavaScript</li>
                </ul>
                <div class="project-image">
                    <figure>
                        <img src="ecommerce.jpg" alt="E-commerce project screenshot">
                        <figcaption>Homepage of the e-commerce site</figcaption>
                    </figure>
                </div>
                <p>Visit the project: <a href="https://example-ecommerce.com" target="_blank">Example Store</a></p>
            </article>

            <article>
                <h3>Weather App</h3>
                <p>A real-time weather application:</p>
                <ol>
                    <li>Fetches API data</li>
                    <li>Displays 5-day forecast</li>
                    <li>Interactive UI</li>
                </ol>
                <svg width="100" height="100">
                    <circle cx="50" cy="50" r="40" stroke="blue" stroke-width="4" fill="lightblue" />
                </svg>
                <canvas id="weatherChart" width="200" height="100"></canvas>
            </article>
        </section>

        <section id="skills">
            <h2>Skills & Technologies</h2>
            <table>
                <tr>
                    <th>Skill</th>
                    <th>Level</th>
                    <th>Years of Experience</th>
                </tr>
                <tr>
                    <td>HTML5</td>
                    <td>Expert</td>
                    <td>5</td>
                </tr>
                <tr>
                    <td>CSS3</td>
                    <td>Advanced</td>
                    <td>4</td>
                </tr>
                <tr>
                    <td>JavaScript</td>
                    <td>Advanced</td>
                    <td>4</td>
                </tr>
            </table>
        </section>

        <section id="contact">
            <h2>Contact Me</h2>
            <form action="/submit-form" method="POST">
                <fieldset>
                    <legend>Personal Information</legend>
                    <label for="name">Name:</label>
                    <input type="text" id="name" name="name" required><br><br>

                    <label for="email">Email:</label>
                    <input type="email" id="email" name="email" required><br><br>

                    <label for="phone">Phone:</label>
                    <input type="tel" id="phone" name="phone"><br><br>

                    <label for="contact-method">Preferred Contact Method:</label>
                    <select id="contact-method" name="contact-method">
                        <optgroup label="Electronic">
                            <option value="email">Email</option>
                            <option value="phone">Phone</option>
                        </optgroup>
                        <option value="none">No preference</option>
                    </select>
                </fieldset>

                <fieldset>
                    <legend>Project Details</legend>
                    <label for="project-type">Project Type:</label>
                    <input list="project-types" id="project-type" name="project-type">
                    <datalist id="project-types">
                        <option value="Website">
                        <option value="Web Application">
                        <option value="E-commerce">
                        <option value="Other">
                    </datalist><br><br>

                    <label>Services Needed:</label><br>
                    <input type="checkbox" id="design" name="services" value="design">
                    <label for="design">Design</label><br>
                    <input type="checkbox" id="development" name="services" value="development">
                    <label for="development">Development</label><br>
                    <input type="checkbox" id="seo" name="services" value="seo">
                    <label for="seo">SEO</label><br><br>

                    <label>Budget Range:</label><br>
                    <input type="radio" id="budget1" name="budget" value="500-1000">
                    <label for="budget1">$500-$1000</label><br>
                    <input type="radio" id="budget2" name="budget" value="1000-5000">
                    <label for="budget2">$1000-$5000</label><br>
                    <input type="radio" id="budget3" name="budget" value="5000+">
                    <label for="budget3">$5000+</label><br><br>

                    <label for="timeline">Project Timeline (weeks):</label>
                    <input type="range" id="timeline" name="timeline" min="1" max="12"><br><br>

                    <label for="color">Brand Color:</label>
                    <input type="color" id="color" name="color" value="#000000"><br><br>

                    <label for="start-date">Preferred Start Date:</label>
                    <input type="date" id="start-date" name="start-date"><br><br>

                    <label for="meeting-time">Best Time for Meeting:</label>
                    <input type="time" id="meeting-time" name="meeting-time"><br><br>

                    <label for="message">Project Details:</label><br>
                    <textarea id="message" name="message" rows="4" cols="50"></textarea><br><br>

                    <label for="file">Upload Project Brief:</label>
                    <input type="file" id="file" name="file"><br><br>

                    <label for="password">Create Account Password (optional):</label>
                    <input type="password" id="password" name="password"><br><br>

                    <label for="quantity">Number of Pages:</label>
                    <input type="number" id="quantity" name="quantity" min="1" max="50"><br><br>

                    <label for="website">Your Website URL:</label>
                    <input type="url" id="website" name="website"><br><br>

                    <output name="result" for="quantity"></output><br><br>

                    <button type="submit">Submit</button>
                    <button type="reset">Reset</button>
                </fieldset>
            </form>

            <address>
                You can reach me at:<br>
                123 Web Dev Street<br>
                San Francisco, CA 94107<br>
                Email: <a href="mailto:jane@example.com">jane@example.com</a><br>
                Phone: <a href="tel:+15551234567">(555) 123-4567</a>
            </address>

            <iframe src="https://maps.example.com/embed" width="400" height="300" frameborder="0" style="border:0;" allowfullscreen=""></iframe>
        </section>

        <section id="media">
            <h2>Media</h2>
            <audio controls>
                <source src="audio.mp3" type="audio/mpeg">
                <track src="subtitles_en.vtt" kind="subtitles" srclang="en" label="English">
                Your browser does not support the audio element.
            </audio>

            <video width="320" height="240" controls>
                <source src="video.mp4" type="video/mp4">
                <source src="video.ogg" type="video/ogg">
                Your browser does not support the video tag.
            </video>

            <object data="sample.pdf" type="application/pdf" width="300" height="200">
                <embed src="sample.pdf" type="application/pdf">
                <p>Unable to display PDF file. <a href="sample.pdf">Download</a> instead.</p>
            </object>
        </section>
    </main>

    <footer>
        <p>&copy; <span id="year"></span> Jane Doe. All rights reserved.</p>
        <p>Connect with me:</p>
        <ul>
            <li><a href="https://linkedin.com">LinkedIn</a></li>
            <li><a href="https://github.com">GitHub</a></li>
            <li><a href="https://twitter.com">Twitter</a></li>
        </ul>
        <p><small>Last updated: <time datetime="2023-04-15">April 15, 2023</time></small></p>
        <hr>
        <p><s>This website was built with WordPress</s> This is a custom-built website.</p>
        <p><code>console.log('Hello World!');</code></p>
        <pre>
            function greet() {
                return "Welcome to my portfolio!";
            }
        </pre>
        <p>Here's an inline quote: <q>Quality is not an act, it is a habit.</q></p>
        <p>The <i>HTML</i> elements used in this page are <b>semantic</b> and <u>accessible</u>.</p>
        <p>Press <kbd>Ctrl</kbd> + <kbd>S</kbd> to save this page.</p>
        <wbr>ThisIsALongWordThatMightBreakAtAnyPoint
    </footer>

    <noscript>
        <p>Your browser does not support JavaScript or it is disabled. Some features may not work properly.</p>
    </noscript>

    <script>
        document.getElementById('year').textContent = new Date().getFullYear();
        
        // Canvas drawing example
        const canvas = document.getElementById('weatherChart');
        if (canvas.getContext) {
            const ctx = canvas.getContext('2d');
            ctx.fillStyle = 'rgb(200, 0, 0)';
            ctx.fillRect(10, 10, 50, 50);
            ctx.fillStyle = 'rgba(0, 0, 200, 0.5)';
            ctx.fillRect(30, 30, 50, 50);
        }
    </script>
</body>
</html>
```

## Key Features of This Project:

1. **Complete HTML Structure**: Includes all required elements from `<!DOCTYPE>` to `</html>`

2. **Semantic HTML**: Uses semantic elements like `<header>`, `<footer>`, `<main>`, `<section>`, `<article>`, and `<nav>`

3. **Forms**: Comprehensive form with all input types including:
   - Text, email, tel, url, password
   - Checkboxes and radio buttons
   - Range, color, date, time, number
   - File upload
   - Select dropdowns with optgroups
   - Datalist for autocomplete

4. **Multimedia**: Includes `<audio>`, `<video>`, `<svg>`, `<canvas>`, and `<iframe>`

5. **Text Formatting**: Demonstrates all text-related elements like:
   - Headings (`<h1>` to `<h6>`)
   - Paragraphs (`<p>`)
   - Text formatting (`<strong>`, `<em>`, `<i>`, `<b>`, `<u>`, `<s>`)
   - Code (`<code>`, `<pre>`)
   - Quotes (`<blockquote>`, `<q>`, `<cite>`)

6. **Lists and Tables**: Includes both ordered/unordered lists and a data table

7. **Interactive Elements**: Features `<details>`, `<summary>`, `<progress>`, and `<meter>`

8. **Metadata**: Proper use of `<meta>` tags, `<title>`, and `<link>`

9. **Accessibility**: Uses proper labeling, alt text for images, and semantic structure

10. **Scripting**: Includes both `<script>` and `<noscript>` elements

This project provides a practical example of how all these HTML elements work together in a real website, while also creating a functional portfolio template that could be used as a starting point for a real portfolio site.