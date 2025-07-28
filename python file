# Description: This script extracts the Table of Contents (ToC) from a USB PD  and converts it into a structured JSONL .

# Required libraries
import pdfplumber as pdfplu
import re
import json

doctitle = "USB Power Delivery Specification Rev X"
path = r"C:\Users\Sanjana\Downloads\USB_PD.pdf"
output = "usb_pd_spec.jsonl"
pagerange = (12, 26)

# Common lines to skip

noise_lines = {
    "Table of Contents",
    "Universal Serial Bus",
    doctitle,
}

# Reading the document and extracting the ToC

def toc_text(path, pagerange):
    toc_lines = []
    with pdfplu.open(path) as pdf:
        for i in range(pagerange[0], pagerange[1]):
            page = pdf.pages[i]
            text = page.extract_text()
            if text:
                lines = text.split('\n')
                for line in lines:
                    clean = line.strip()
                    if clean and clean not in noise_lines:
                       clean = re.sub(r'\.{2,}', ' ', clean)  
                       toc_lines.append(clean.strip())
    return toc_lines

# Extracting the structured information from toc

def parse_toc(line):
    pattern = r"^(\d+(?:\.\d+)*)(?:\s+)(.*?)(?:\.+\s+|\s{2,})(\d+)$"
    match = re.match(pattern, line.strip())

    if match:
        sectionid = match.group(1)
        title = match.group(2).strip()
        page = int(match.group(3))
        level = sectionid.count('.') + 1
        fullpath = f"{sectionid} {title}"

        if '.' in sectionid:
            parts = sectionid.split('.')
            parentparts = parts[:-1]
            parentid = '.'.join(parentparts)
        else:
            parentid = None

        return {
            "doc_title": doctitle,
            "section_id": sectionid,
            "title": title,
            "page": page,
            "level": level,
            "parent_id": parentid,
            "full_path": fullpath,
        }
    return None

# Generating the JSONL file

def jsonl_file(extracted_text, output):
    seen_ids = set()
    count = 0
    with open(output, 'w', encoding='utf-8') as f:
        for line in extracted_text:
            parsed = parse_toc(line)
            if parsed:
                sid = parsed["section_id"]
                if sid not in seen_ids:
                    f.write(json.dumps(parsed) + '\n')
                    seen_ids.add(sid)
                    count += 1

# Main program
extractedtext = toc_text(path, pagerange)
jsonl_file(extractedtext, output)
