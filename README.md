# best vps for n8n: real RAM requirements, what $6–$24/mo actually buys, and a no-lock-in way to test your setup first

n8n is one of the least demanding self-hosted tools you can put on a server. It's a Node.js application that idles at roughly 100–500 MB of memory and only spikes when a workflow actually runs. Almost any VPS from a serious provider can host it, which means picking the best VPS for n8n is mostly about three things: how much RAM your workflows genuinely need, where the server sits relative to the apps it talks to, and how the pricing behaves after month one.

The short answer: 2 vCPU and 4 GB of RAM is the sweet spot for a small business running dozens of workflows. If you're starting out, a 1–2 GB plan in the $6–12/month range is enough. And if you want to rehearse a complete setup — Docker, PostgreSQL, HTTPS, backups — before committing to a long contract, Vultr's hourly billing and signup credit make that unusually cheap to do.

> **Quick picks**
>
> - Most small production setups: Vultr's 2 GB cloud compute plan at **$12/mo** — flat rate, hourly billing, 55 GB SSD, 2 TB transfer. 👉 [Check Vultr's current cloud compute pricing and signup credit](https://www.vultr.com/pricing/?ref=7268283)
> - Testing or a handful of simple workflows: the 1 GB plan at $6/mo will run it.
> - Heavy webhooks, large files, or AI steps: the 4 GB plan at $24/mo, or split PostgreSQL onto its own box.
> - Never touched a terminal? Hostinger's one-click n8n template is the easier start — but its promo price roughly doubles on renewal, which matters once you do the math over two years.

## What the best VPS for n8n actually needs

RAM. n8n's own documentation makes the same point: memory needs outrank CPU needs. The app itself barely sips resources at idle, but each execution holds its data in memory while it runs — a workflow pulling a large spreadsheet or a chunky JSON response can consume hundreds of megabytes on its own, and a complex execution can add 1–2 GB on top. Once you add PostgreSQL for production storage, budget another 512 MB to 1 GB.

That's why a 2 GB box that performs fine in testing starts swapping the moment three workflows fire simultaneously. CPU rarely becomes the limit unless you're running AI models locally or doing image processing inside Code nodes; for typical webhook handling and database reads, two vCPUs handle a lot of throughput.

| Your use case | vCPU | RAM | Storage |
| --- | --- | --- | --- |
| Testing, a few simple workflows | 1–2 | 2 GB | 20 GB |
| Small business, dozens of workflows | 2 | 4 GB | 40 GB |
| Heavy use: big files, AI steps, high webhook volume | 2–4 | 8 GB | 80 GB+ |

One storage caveat people discover the hard way: execution history grows quietly in the background. Turn on n8n's execution data pruning early, or the database keeps expanding until the disk fills. A sensible default is keeping successful executions for about 7 days and failed ones for about 30.

## Vultr's cloud compute plans for n8n

For n8n, the relevant part of Vultr's catalog is the shared-CPU Cloud Compute line. Three tiers cover nearly every self-hosted setup, from first experiments to a small business running automation as real infrastructure:

| Vultr plan | vCPU | RAM | Storage | Transfer | Monthly price |
| --- | --- | --- | --- | --- | --- |
| [ Get Cloud Compute – 1 GB](https://www.vultr.com/pricing/?ref=7268283) | 1 | 1 GB | 25 GB SSD | 1 TB | $6 |
| [ Get Cloud Compute – 2 GB](https://www.vultr.com/pricing/?ref=7268283) | 1 | 2 GB | 55 GB SSD | 2 TB | $12 |
| [ Get Cloud Compute – 4 GB](https://www.vultr.com/pricing/?ref=7268283) | 2 | 4 GB | 80 GB SSD | 3 TB | $24 |

All three are billed monthly at a flat rate with hourly metering underneath — you pay only while the instance exists, and there's no long-term contract behind the price. That structure matters more than it sounds. Some competitors advertise a low intro rate that roughly doubles at renewal; with Vultr, the listed monthly rate is what you keep paying, month after month.

New accounts also come with signup credit — typically a few hundred dollars' worth, though the exact amount moves with current promotions, so check it at signup. Combined with hourly billing, that credit effectively functions as a free rehearsal window: build the whole thing, break it, rebuild it, and pay nothing while you learn. 👉 [Open Vultr's pricing page to pick a plan and region](https://www.vultr.com/pricing/?ref=7268283)

Above the shared-CPU line, Vultr sells dedicated-core Optimized Cloud Compute instances and bare metal servers (bare metal starts around $120/mo). Almost nobody needs either for n8n. If you outgrow a single box, the better move is horizontal: once you cross roughly 50 concurrent workflow executions or your PostgreSQL data pushes past 20 GB, move the database to its own small server. Two 4 GB machines outperform one 8 GB machine for n8n at scale, because you isolate memory and disk contention.

## Where Vultr wins for n8n — and where it doesn't

Vultr's strongest arguments are pricing structure and location coverage; its weak spots are the dashboard and support.

**The good parts:**

- **Flat pricing with no lock-in.** Hourly billing means you can deploy a server, test a workflow for a day, destroy it, and pay pennies. No 48-month commitment to unlock the decent rate.
- **32 data center locations worldwide.** Region choice is genuinely important for n8n: webhooks fire from services like Stripe or GitHub, and a server near those services shaves latency off every triggered run. US-heavy automation stack? Pick a US region. EU clients and GDPR concerns? Amsterdam or Frankfurt.
- **The credit-funded trial run.** Because instances bill hourly, signup credit can cover your entire learning phase — including the backup restore test described further down — before a single invoice arrives.

👉 [Spin up a Vultr instance while the signup credit lasts](https://bit.ly/VulTR)

**The honest downsides:** the control panel takes longer to feel at home in than Hostinger's or DigitalOcean's beginner-oriented dashboards, and support response times vary. Also, unlike Hostinger or DigitalOcean, there's no polished one-click n8n template to lean on — you'll be following a Docker Compose setup yourself. That's maybe 30–45 minutes of work the first time, and it leaves you with a cleaner, more portable installation than most templates produce. Whether that trade suits you depends on your tolerance for a terminal window.

## Vultr vs other popular n8n hosts

Every mainstream provider here can run n8n without trouble. The real differences are price per gigabyte of RAM, regional coverage, and how much setup work is done for you:

| Provider | Example n8n-friendly plan | Price | Standout points | Watch out for |
| --- | --- | --- | --- | --- |
| Vultr | 2 vCPU, 4 GB, 80 GB SSD | $24/mo flat | 32 global regions, hourly billing, signup credit | Dashboard less beginner-friendly; support varies |
| Hetzner | CX22: 2 vCPU, 4 GB, 40 GB NVMe | ~$4–5/mo | 20 TB traffic in the EU; best price-to-performance | US locations capped at 1 TB traffic; no managed services or uptime SLA |
| Hostinger | KVM 2: 2 vCPU, 8 GB, 100 GB NVMe | ~$7–9/mo promo | One-click n8n template; very easy start | Renewals roughly double; best price needs a long commitment |
| DigitalOcean | 1–2 GB Droplet | from ~$6/mo | $200 signup credit; excellent docs and tutorials | Costs more per GB of RAM |
| Contabo | 4 vCPU, 8 GB | ~€4.50–5/mo | Most RAM per euro | Disk trails Hetzner's in third-party benchmarks |

Reading that table honestly: Hetzner is hard to beat if your audience and services are Europe-based. DigitalOcean earns its premium with documentation and a marketplace template. Contabo suits workflows that shovel large files around. Hostinger removes nearly all setup friction, at the cost of renewal pricing you should model before clicking buy. Vultr's edge is flexibility — flat rates, hourly billing, and the widest region selection of the group, which matters the moment your webhooks span continents.

## Setting up n8n on a Vultr server

The fastest reliable path is Docker Compose with PostgreSQL behind a reverse proxy. First time, budget an afternoon; after that, under an hour. The order that avoids rework:

1. Create the server on Ubuntu 24.04 in the region nearest the apps your workflows talk to, with an SSH key added at creation.
2. Harden it: a normal sudo user, password logins disabled, a firewall allowing only ports 22, 80 and 443, automatic security updates on.
3. Install Docker and Docker Compose.
4. Point a subdomain — say, automations.yourdomain.com — at the server's IP.
5. Run n8n with PostgreSQL rather than the default SQLite. Postgres handles concurrent executions and growing histories far more reliably.
6. Put a reverse proxy (Caddy, Nginx or Traefik) in front for HTTPS. Webhooks from Stripe, GitHub and similar services require a valid HTTPS URL — plain HTTP will silently break them.
7. Set the n8n encryption key yourself and store a copy somewhere safe, ideally off the server.
8. Enable execution pruning so the database doesn't grow without bound.

That encryption key deserves its own warning, because most setup guides skip it. n8n encrypts every credential you save — API keys, OAuth tokens, passwords — with that key. Rebuild the server without it and every stored credential becomes unreadable; you'd be reconnecting every single app by hand. So back up three things: the database, the encryption key, and your Docker Compose file. Export your workflows as JSON regularly too.

Then do one restore test before you trust any of it. Spin up a second cheap server, restore the database and the key, log in, and confirm your credentials actually work. Twenty minutes of checking beats discovering during an outage that your backups were decorative. Hourly billing makes this test nearly free. 👉 [Deploy a Vultr server for your n8n install](https://www.vultr.com/pricing/?ref=7268283)

## n8n VPS FAQ

**How much RAM does n8n need?**
2 GB works for light use and testing. 4 GB is the realistic production baseline for a small business. Go to 8 GB if you process large files, run many workflows concurrently, or call AI models from your workflows.

**Can you run n8n on a $6 VPS?**
For experimenting, yes — a 1 GB plan will boot n8n and handle a few simple workflows. But once PostgreSQL and real production workloads join, 1 GB runs out of headroom fast. For anything you depend on, start at 2 GB.

**Self-hosted or n8n Cloud?**
n8n Cloud starts at $20/mo with execution limits and zero server work. A 2 GB self-hosted VPS runs $12/mo with unlimited executions, and even Vultr's 4 GB plan at $24/mo costs about the same as Cloud's entry tier while letting you run as many workflows as the hardware handles. Choose Cloud only if you never want to see a terminal; the maintenance burden is the actual price of self-hosting.

**Docker or native install?**
Docker. Updates, backups, and isolation from the host system are all simpler, and moving to a bigger server later becomes a file copy rather than an archaeology project.

## Which Vultr plan to pick, based on your situation

- **Just exploring n8n:** the 1 GB plan at $6/mo, hourly billing, destroy it when you're done. Total exposure: pocket change.
- **Real automations for a small business:** the 2 GB plan at $12/mo with PostgreSQL and execution pruning turned on from day one.
- **Heavy webhooks, large files, or local AI steps:** the 4 GB plan at $24/mo — and if you cross ~50 concurrent executions, add a second small server for the database instead of upsizing.
- **Zero tolerance for the command line:** a host with a one-click n8n template will serve you better than any manual setup; just price out the renewal before committing to a long term.

Two things to settle before you click buy: pick the region closest to the services your workflows call, and confirm the current signup credit amount — 👉 [Vultr's pricing page shows both](https://www.vultr.com/pricing/?ref=7268283).
