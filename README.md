# Storage VPS: What a Terabyte Really Costs, How to Pick One for Backups, Nextcloud or Media, and Where Vultr Fits In

A storage VPS is a virtual private server you buy for its disk instead of its CPU. It's the machine that holds your restic or Borg backups, runs Nextcloud, keeps a Plex or photo library alive, or parks years of compressed logs. The number that separates every offer on the market is the cost per terabyte, and that number currently ranges from roughly €2–3/TB on bulk HDD plans to $30+/TB on NVMe servers — so picking the wrong shape of storage can cost you ten times more for the same capacity.

This guide breaks down the real per-TB pricing across the providers selling storage VPS plans as of October 2026, lists the limits that only appear in their help docs, and then covers how Vultr approaches the same job. Vultr doesn't sell a plan labeled "storage VPS" — you assemble one from its catalog in a few minutes — and for some storage workloads that's the better tool anyway.

> **Quick picks**
>
> - Cheapest real terabytes on a root server: **AlphaVPS** — 16 TB on RAID-60 HDD with an NVMe read cache for **€53.33/month** (annual billing, €3.33/TB), in Sofia, Nuremberg, London or Dallas.
> - Cheapest attachable disk: **BuyVM's** block storage slabs — 256 GB for $1.25/month, about $5/TB.
> - Cheapest no-server backup target: **Hetzner's** Storage Box — 1 TB from €3.20/month, protocol access only.
> - Privacy-first checkout: **NovaVPS** — 1 TB NVMe root server from $30/month, no KYC, Monero and other crypto accepted.
> - One platform for instances, attachable volumes and S3-compatible storage in 32 locations: [👉 check Vultr's current plan pricing](https://www.vultr.com/pricing/?ref=7268283) — instances from $2.50/month, block storage at $1 per 10 GB.

## What a storage VPS is — and when you don't need one

A storage VPS is a KVM virtual machine where disk capacity is the headline spec: full root access, your own filesystem, and enough space that the usual 25–80 GB VPS disk would be a joke. Typical workloads are write-once, keep-forever jobs — backup repositories, Nextcloud, media libraries, log archives — where read speed matters less than not losing data.

Three product shapes get sold under this label, and they behave differently:

1. **A root server with a big disk.** AlphaVPS, Contabo, Cloudzy and NovaVPS all sell this. You get a shell, install whatever you want, mount the disk, run cron jobs.
2. **An attachable volume.** BuyVM's slabs and Vultr's block storage are extra disks that plug into an existing server. You can't buy the disk alone — there has to be an instance to attach it to.
3. **A protocol-only box or bucket.** Hetzner's Storage Box is a mounted network share you reach over SFTP or rsync, not a server you log into. Object storage (S3 buckets) sits in the same category: API access, no shell.

That third category is worth a hard look before you buy. If your apps only ever speak S3, a bucket is cheaper and simpler. The server shape earns its price when you need to run software — Nextcloud, Plex, PhotoPrism, a deduplicated backup repo with retention policies — next to the bytes.

## What a terabyte actually costs right now

Bulk HDD storage with a root server now lands around €3.33/TB, attachable HDD slabs near $5/TB, and true NVMe root servers start around $30/TB. Hyperscaler object storage sits near $23/TB before you pay a cent of egress. Here's the field, using published list prices:

| Storage shape | Example offer | Media | Effective cost per TB | The catch |
| --- | --- | --- | --- | --- |
| Bulk root server | AlphaVPS 16 TB plan (€53.33/mo, annual) | HDD RAID-60 + NVMe cache | €3.33/TB | Flat rate needs annual billing; 4 locations |
| Protocol backup box | Hetzner Storage Box, 20 TB (€40.60/mo) | HDD | ≈€2.03/TB | No root shell; identity verification required |
| Attachable slab | BuyVM 1 TB slab (≈$5/mo) | HDD + NVMe cache | ≈$5/TB | Must already run their VPS; 3 datacenters |
| Backup-focused cloud | dedicated backup providers | varies | ~$6–7/TB | Storage only — no CPU, no shell |
| NVMe root server | NovaVPS Storage 1 ($30/mo) | NVMe | $30/TB | The NVMe premium; 21 locations |
| NVMe storage VPS | Cloudzy (from $7.48/mo for 60 GB) | NVMe, up to 1.5 TB | priced per plan | Capacity tops out at 1.5 TB |
| Attachable cloud volume | Vultr block storage ($1 per 10 GB) | network volume | ≈$100/TB | Needs a Vultr instance in the same region |
| Hyperscaler object storage | S3-class buckets | object | ~$23/TB | Egress fees on top — AlphaVPS's own price comparison puts them around $90/TB |

Two things to take from that table. First, the cheap end of the market is HDD with an NVMe cache layer, and for archive jobs that's usually fine — AlphaVPS runs two parity drives per RAID-6 set and swaps failing disks proactively, with a 99.9% SLA behind it. Second, egress is where flat-rate providers quietly win: AlphaVPS bundles ports up to 10 Gbit with 3–40 TB of monthly transfer depending on plan, Contabo advertises unlimited traffic with a fair-use clause, and Vultr includes a monthly bandwidth pool with each instance — the $5 tier gets 1 TB — with per-GB billing beyond it.

## How Vultr handles storage VPS workloads: three building blocks

Vultr sells no dedicated storage SKU. You build the equivalent from three products: a Cloud Compute instance (the actual server), attachable Block Storage volumes (capacity you add on top), and S3-compatible Object Storage (for workloads that talk the S3 API). Deploy is a few minutes through the control panel, API, or CLI, hourly billing means you can spin something up for a weekend ingest and delete it after, and always-on DDoS protection comes included at no extra charge.

The practical upside is regional spread — 32 locations across six continents — and the fact that compute and storage live under one API. The practical downside shows up in the next table: this is not the cheap-terabytes platform, and pretending otherwise would waste your money.

## Vultr Cloud Compute plans for storage workloads

Every price rung below exists in two flavors at the same monthly cost: **Regular Performance** (Intel, SSD) and **High Performance** (AMD EPYC, NVMe). Pick High Performance if your storage workload reads as much as it writes — MinIO, CI artifact caches, PhotoPrism libraries.

| Plan | RAM | Built-in storage | Monthly bandwidth | Price/mo | Link |
| --- | --- | --- | --- | --- | --- |
| Regular Cloud Compute — entry | 512 MB | 10 GB SSD | 0.5 TB | $2.50 | [ Deploy](https://www.vultr.com/pricing/?ref=7268283) |
| Small | 1 GB | 25 GB | 1 TB | $5.00 | [ Deploy](https://www.vultr.com/pricing/?ref=7268283) |
| Standard | 2 GB | 55 GB | 2 TB | $10.00 | [ Deploy](https://www.vultr.com/pricing/?ref=7268283) |
| Mid | 4 GB | 80 GB | 3 TB | $20.00 | [ Deploy](https://www.vultr.com/pricing/?ref=7268283) |
| Heavy | 8 GB | 160 GB | 4 TB | $40.00 | [ Deploy](https://www.vultr.com/pricing/?ref=7268283) |
| Multi-service | 16 GB | 320 GB | 5 TB | $80.00 | [ Deploy](https://www.vultr.com/pricing/?ref=7268283) |
| Large | 32 GB | 640 GB | 6 TB | $160.00 | [ Deploy](https://www.vultr.com/pricing/?ref=7268283) |
| Top of the standard ladder | 64 GB | 1.28 TB | 10 TB | $320.00 | [ Deploy](https://www.vultr.com/pricing/?ref=7268283) |

CPU scales with the rungs: 1 vCPU through the 2 GB tier, 2 vCPUs at 4 GB, 4 at 8 GB, 6 at 16 GB. The ladder continues above the table with a 128 GB / 2.56 TB NVMe tier at $640/month. Notice the pattern: even the biggest standard instance carries 1.28 TB of built-in disk. For a storage VPS, that cap is exactly why the next section exists.

## Adding capacity: block storage and object storage

Block storage is how you get real terabytes on Vultr. Volumes run from 10 GB to 10 TB per volume at a flat $1 per 10 GB, attach to an instance in the same region, format as ext4 or XFS, and behave like any other mounted disk. [👉 Open Vultr's block storage page to size a volume](https://www.vultr.com/products/block-storage/?ref=7268283)

| Volume size | Price/mo | Link |
| --- | --- | --- |
| 100 GB | $10 | [ Add storage](https://www.vultr.com/products/block-storage/?ref=7268283) |
| 500 GB | $50 | [ Add storage](https://www.vultr.com/products/block-storage/?ref=7268283) |
| 1 TB | $100 | [ Add storage](https://www.vultr.com/products/block-storage/?ref=7268283) |
| 2 TB | $200 | [ Add storage](https://www.vultr.com/products/block-storage/?ref=7268283) |
| 10 TB | $1,000 | [ Add storage](https://www.vultr.com/products/block-storage/?ref=7268283) |

If your workload speaks S3 instead of needing a filesystem, Object Storage skips the server question entirely: $5/month gets you 250 GB of storage plus 1 TB of bandwidth with an S3-compatible API, and storage beyond that is billed per gigabyte. [👉 See Vultr's object storage plans](https://www.vultr.com/products/object-storage/?ref=7268283) That works out to $20/TB — cheaper than the hyperscaler buckets, without their egress metering on the included transfer.

## When Vultr makes sense for storage — and when it doesn't

Vultr makes sense when flexibility, speed, or geography matter more than raw terabytes. It doesn't make sense when cost per TB is the whole metric, and the math here is blunt: 4 TB via Vultr block storage runs $400/month, while AlphaVPS sells a 4 TB root server for €13.33/month on annual billing. If you're building a two-to-sixteen-terabyte backup box, the HDD specialists win that fight by a factor of twenty or more, and no honest comparison says otherwise.

Where Vultr earns its price:

- **NVMe speed on small-to-medium datasets.** A 1–2 TB MinIO or restic target on High Performance instances behaves completely differently than an HDD array with a cache.
- **Storage next to your compute.** If your apps already run on Vultr, attaching a volume in the same region over private networking beats shuffling data across providers.
- **Automation.** Full API and CLI coverage, hourly billing, deploy-and-destroy workflows for temporary bulk ingest.
- **Coverage.** 32 locations means the backup target can sit in the same country as the machines backing up to it, which keeps restore times sane.

For that kind of setup, [👉 compare Vultr's cloud compute plans](https://www.vultr.com/products/cloud-compute/?ref=7268283) and pick the rung your RAM needs, then attach capacity separately.

## Fine-print limits to check before you buy

These are the details that live in help docs and footnotes, and several of them change the real cost:

- **Contabo's** advertised rates are first-term pricing. Storage VPS 10 shows €4.40/month for the first 24 months, then renews at €5.50; the 400 GB Storage VPS 20 (their "Unbeatable Value" pick, 3 vCPU / 8 GB RAM) runs €6.00 first-term, €7.50 after. Changing to a different storage plan or storage type requires a server reinstall — back up everything first. Storage is SSD, not NVMe, and outbound traffic falls under a fair-use policy.
- **AlphaVPS's** flat €3.33/TB assumes annual billing; the 1 TB plan is €4.00/month if you pay monthly. That's still cheap, just not the headline number.
- **Hetzner's** Storage Box has no root shell and requires identity verification, so it's out if a mountable share doesn't cover your use case.
- **BuyVM's** slabs can't be bought standalone — you need one of their VPS plans first, and capacity sits on HDD with NVMe cache.
- **NovaVPS's** no-KYC signup (token-only or email plus a 12-character password) is the product; the $30/TB price is what you trade for it.
- **Vultr's** volumes attach only to instances in the same region and are data disks rather than boot disks. Bandwidth beyond each plan's pool is billed per GB, so a backup target that pushes terabytes monthly deserves a quick egress estimate first.

## FAQ

**Is a storage VPS the same thing as object storage?**
No. A storage VPS gives you a root filesystem you can mount, partition, and run any software on; object storage gives you an S3 API and nothing else. Backup tools like restic and Borg work with either, but Nextcloud and Plex need the server.

**How much bandwidth do backups actually use?**
The initial sync is the expensive part; incrementals after that are usually a small fraction of your dataset. Contabo's unlimited-inbound stance and AlphaVPS's 10 Gbit ports both make the first sync painless, while on Vultr you'd want the plan's bandwidth pool to cover roughly your initial upload plus headroom.

**HDD or NVMe for a backup server?**
HDD is fine for write-once archives — AlphaVPS and BuyVM both layer an NVMe read cache over HDD arrays, so restore reads feel faster than raw disk. NVMe earns its premium when the storage is in a hot path: S3-compatible endpoints, CI caches, anything with many small random reads.

**Can I run Nextcloud or Plex on these plans?**
Yes, and both want more CPU than a pure archive box. Contabo's Storage VPS 20 with 3 vCPUs and 8 GB RAM is their recommended middle ground; NovaVPS's $30 tier pairs 1 TB of NVMe with 8 GB of ECC RAM. On Vultr, the 4–8 GB Cloud Compute tiers with an attached volume cover the same job.

## Final picks before you click buy

If the goal is the most terabytes per euro on a real root server, AlphaVPS's 16 TB plan at €53.33/month is the current benchmark. If you just need a cheap disk bolted onto an existing small server, BuyVM's $1.25 slabs do that job. If payment privacy is the priority, NovaVPS's no-KYC crypto checkout is the reason it exists.

If you'd rather have one platform that handles the servers, the volumes, and the S3 bucket under a single API — with NVMe on demand and hourly billing that makes a wrong-sized instance cost pennies to abandon — [👉 deploy a Vultr cloud compute instance and attach your first block storage volume](https://www.vultr.com/pricing/?ref=7268283). Before you do, run two quick numbers: your expected monthly upload (so bandwidth overage doesn't surprise you), and your restore plan — a backup target you've never tested a restore from is just a wish.
