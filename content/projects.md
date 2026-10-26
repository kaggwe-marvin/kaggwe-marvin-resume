+++
title = "Projects"
description = "Software and websites built by Kaggwe Marvin: Voucher Manager for MikroTik RouterOS, FoundUG, Penzi's Cocktail Bar and more."
template = "projects.html"

[extra]
intro = "Things I have built and shipped. Software downloads always point to the latest release on GitHub."

# Software with GitHub releases. The page reads each one's latest release.
[[extra.apps]]
repo = "kaggwe-marvin/Voucher-Manager"
name = "Voucher Manager"
platform = "Windows 10 and 11"
category = "UtilitiesApplication"
description = "Batch-create MikroTik User Manager hotspot vouchers and print them as a PDF. It connects to your router, creates the accounts, downloads the vouchers and converts them to a printable file."
post = "blog/voucher-manager-routeros-7.md"
note = "Not code-signed yet, so Windows may show \"Windows protected your PC\". Click More info, then Run anyway."

# Live websites. Only list sites that are online.
[[extra.sites]]
name = "Resume and portfolio"
url = "https://resume.kaggwemarvinvictor.me"
description = "My main site: full resume, blog, websites and software downloads."
stack = ["Zola", "HTML", "CSS"]

[[extra.sites]]
name = "FoundUG"
url = "https://foundug.vercel.app"
description = "A free platform for reporting lost and found documents in Uganda and returning them to their owners."
stack = ["Next.js", "TypeScript", "Supabase"]
source = "https://github.com/kaggwe-marvin/foundug"

[[extra.sites]]
name = "Penzi's Cocktail Bar"
url = "https://kaggwe-marvin.github.io/penzi"
description = "Website for a cocktail bar, with its menu of signature drinks, an about page and event bookings."
stack = ["HTML", "CSS"]
source = "https://github.com/kaggwe-marvin/penzi"

# Code on GitHub
[[extra.code]]
name = "Custom ERP"
url = "https://github.com/kaggwe-marvin/custom-ERP"
description = "Modular Django ERP with a double-entry ledger that blocks unbalanced entries, role-based access and audit logs."

[[extra.code]]
name = "Student Clearance System"
url = "https://github.com/kaggwe-marvin/student-clearance-system"
description = "Role-based university clearance workflow for students, staff and admins. TypeScript, Hono and Drizzle ORM."

[[extra.code]]
name = "Inventory Tracker"
url = "https://github.com/kaggwe-marvin/Django-Inventory-tracker"
description = "Django stock tracker with low-stock alerts, CSV reports and a rate-limited REST API."
+++
