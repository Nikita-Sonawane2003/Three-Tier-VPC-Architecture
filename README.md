# AWS Three-Tier VPC Architecture

A hands-on AWS project that demonstrates a segmented VPC design for a three-tier application: **web, application, and database**. The architecture uses public subnets for the internet-facing entry point and private subnets for internal application and database resources.

## Architecture Diagram

![Three-tier application architecture](screenshots/01-three-tier-vpc-architecture.png)

## Project Highlights

- Custom Amazon VPC with CIDR `10.0.0.0/16`
- Eight subnets across two Availability Zones (`ap-northeast-2a` and `ap-northeast-2b`)
- Public subnets for the internet-facing Application Load Balancer (ALB)
- Private web subnets with nginx on EC2
- Private application subnets with PHP and PHP-FPM on EC2
- Internal ALB between the web and application tiers
- Private database subnets and Amazon RDS for MySQL
- Security groups intended to restrict traffic between tiers
- Web and application load balancers shown across two Availability Zones
- Custom AMIs named `WebAMI` and `PhpAMI`

## Network Plan

| Tier | Subnet | CIDR |
|---|---|---|
| Public | `Publicsubnet1` | `10.0.0.0/20` |
| Public | `Publicsubnet2` | `10.0.16.0/20` |
| Web | `Websubnet1` | `10.0.32.0/20` |
| Web | `Websubnet2` | `10.0.48.0/20` |
| Application | `Appsubnet1` | `10.0.64.0/20` |
| Application | `Appsubnet2` | `10.0.80.0/20` |
| Database | `DBsubnet1` | `10.0.96.0/20` |
| Database | `DBsubnet2` | `10.0.112.0/20` |

The design leaves `10.0.128.0/17` available for future subnet expansion.

## Request Flow

1. Users access the internet-facing ALB over HTTP port `80`.
2. The public ALB forwards requests to the nginx web tier in private subnets.
3. The web tier forwards eligible PHP requests through the internal ALB.
4. The internal ALB forwards requests to the PHP/PHP-FPM application tier.
5. The application tier connects to Amazon RDS for MySQL on TCP port `3306`.

## AWS Services Used

| Service / Component | Role |
|---|---|
| Amazon VPC | Isolated network boundary |
| Subnets and route tables | Network segmentation and traffic routing |
| Internet Gateway | Internet routing for public subnets |
| Application Load Balancer | Public and internal traffic distribution |
| Amazon EC2 | Hosts nginx and PHP/PHP-FPM tiers |
| Amazon Machine Images (AMIs) | Reusable web and PHP server images |
| EC2 Auto Scaling | Capacity management for web and application tiers |
| Security Groups | Stateful traffic filtering between components |
| Amazon RDS for MySQL | Managed relational database |

## Implementation Summary

1. Created a VPC named `ThreeTierVpc` with CIDR `10.0.0.0/16`.
2. Created public, web, application, and database subnets across two Availability Zones.
3. Set up route tables and attached an Internet Gateway for public-subnet routing.
4. Created security groups for the web and database tiers, plus application-tier security groups.
5. Prepared custom AMIs for the web and PHP application servers.
6. Set up an internet-facing ALB and an internal ALB across two Availability Zones.
7. Created an Amazon RDS for MySQL database and waited for its status to become `Available`.
8. Connected from an EC2 terminal to the RDS endpoint and ran a database query to verify connectivity.

Adjust this summary if any step differs from the final configuration in your AWS account.

## Security Considerations

- Keep application and database resources in private subnets.
- Keep RDS **Public access** disabled.
- Allow database inbound TCP `3306` only from the application tier's security group.
- Allow traffic to each tier only from the expected upstream load balancer or tier.
- Restrict SSH access to trusted IP addresses, or use AWS Systems Manager where configured.
- Use HTTPS (`443`) and a valid TLS certificate for production deployments.
- Store database credentials in a managed secret store; never commit passwords, access keys, private keys, `.env` files, or other secrets to GitHub.
- **Before publishing screenshots:** the terminal screenshot may display personal data and password hashes from a database query. Replace it with a redacted version or remove it from the public repository. Never publish real user records or password hashes.

## Screenshots

| File | Description |
|---|---|
| `01-three-tier-vpc-architecture.png` | High-level three-tier architecture diagram |
| `02-vpc-resource-map.png` | VPC resource map showing subnets, route tables, and Internet Gateway connection |
| `03-vpc-details.png` | VPC details and CIDR configuration |
| `04-subnets-overview.png` | Subnet list and CIDR blocks |
| `05-rds-database-created.png` | RDS database creation confirmation |
| `06-custom-amis-web-and-php.png` | Custom web and PHP AMIs |
| `07-load-balancers-overview.png` | Public and internal load balancers across two AZs |
| `08-web-security-group.png` | Web security group overview |
| `09-rds-mysql-connection-test.png` | Terminal connection and database query test (**redact before public upload**) |
| `10-rds-database-available.png` | RDS database in Available state |

## Repository Structure

```text
three-tier-vpc-architecture/
├── README.md
└── screenshots/
    ├── 01-three-tier-vpc-architecture.png
    ├── 02-vpc-resource-map.png
    ├── 03-vpc-details.png
    ├── 04-subnets-overview.png
    ├── 05-rds-database-created.png
    ├── 06-custom-amis-web-and-php.png
    ├── 07-load-balancers-overview.png
    ├── 08-web-security-group.png
    ├── 09-rds-mysql-connection-test.png
    └── 10-rds-database-available.png
```

## Skills Demonstrated

Amazon VPC, CIDR/subnet planning, public/private network segmentation, route tables, Internet Gateway, Application Load Balancers, EC2, AMIs, Auto Scaling concepts, security groups, Amazon RDS for MySQL, and database connectivity testing.

## Author

**Nikita Sonawane**  
GitHub: [Nikita-Sonawane2003](https://github.com/Nikita-Sonawane2003)
