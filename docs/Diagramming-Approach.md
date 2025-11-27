# Solution Architect's Workflow w/ Copilot

## Approach 1 - Using Copilot & Draw.io

1. Install pandoc for converting documents to Copilot supported format .md
   <br>
    https://github.com/jgm/pandoc/releases/tag/3.8.2.1
2. Convert the requirements document from .docx format to .md format
    <br> 
    `pandoc Requirements.docx -o Requirements.md --extract-media=./images`
3. Install "Draw.io Integration" extension in VS Code to see live preview and make edits to the generated diagram.
4. Provide the converted requirements document in .md format along with relevant images to the context, and ask Copilot to generate the diagram.

---

## Approach 2 - Using Copilot & Mermaid

1. Install pandoc for converting documents to Copilot supported format .md
   <br>
    https://github.com/jgm/pandoc/releases/tag/3.8.2.1
2. Convert the requirements document from .docx format to .md format
    <br> 
    `pandoc Requirements.docx -o Requirements.md --extract-media=./images`
3. Install "Mermaid Chart" extension in VS Code to see live preview and make edits to the generated diagram
4. Provide the converted requirements document in .md format along with relevant images to the context, and ask Copilot to generate the diagram.
5. The generated diagram can be exported to png or svg image format and added to confluence.

### Exporting using CLI.
1. Install Nodejs version 18 or higher.
    <br>
    https://nodejs.org/en/download
2. Install mermaid cli for many export options as shown below.
    <br>
    `npm install -g @mermaid-js/mermaid-cli`

    - #### Export to PNG (default)
        `mmdc -i docs/cloud_architecture.mmd -o docs/cloud_architecture.png`

    -   #### Export to SVG (better for documentation)
        `mmdc -i docs/cloud_architecture.mmd -o docs/cloud_architecture.svg`

    -   #### Export to PDF
        `mmdc -i docs/cloud_architecture.mmd -o docs/cloud_architecture.pdf`

    -   #### Specify background color
        `mmdc -i docs/cloud_architecture.mmd -o docs/cloud_architecture.png -b white`

    -   #### Set custom width/height
        `mmdc -i docs/cloud_architecture.mmd -o docs/cloud_architecture.png -w 1920 -H 1080`

    -   #### High quality output with transparent background
        `mmdc -i docs/cloud_architecture.mmd -o docs/cloud_architecture.png -b transparent -s 3`

    -   #### Use a custom theme
        `mmdc -i docs/cloud_architecture.mmd -o docs/cloud_architecture.png -t dark`

    -   #### Batch convert all .mmd files
        ```
        for file in docs/*.mmd; do
        mmdc -i "$file" -o "${file%.mmd}.png"
        done
        ```
<br>

> **Note:** 
> - We can convert the document to md manually through BroadGPT if the users don't want to use the pandoc CLI tool.
> - For hardening specific requirements analysis approach, diagramming conventions or documentation considerations, use the .github/copilot-instructions.md file to specify those as needed.
