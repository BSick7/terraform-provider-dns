# terraform-provider-dns
[![CircleCI](https://circleci.com/gh/steve-jansen/terraform-provider-dns.svg?style=svg)](https://circleci.com/gh/steve-jansen/terraform-provider-dns)

A terraform plugin that provides read-only resources for DNS records.

## Clone

```bash
mkdir -p $GOPATH/src/github.com/Shopify
cd $GOPATH/src/github.com/Shopify
git clone git@github.com:Shopify/terraform-provider-dns.git
cd terraform-provider-dns
```

## Dependencies

```bash
# Make sure you have glide
go get -u github.com/Masterminds/glide

# Install dependencies
glide install
```

## Installation

Build the binary:

```bash
go build $(realpath `which terraform`-provider-dns)
```

Copy/move the `terraform-provider-dns` binary to your terraform installation
directory, (e.g., `realpath \`which terraform\``,
`realpath -f \``which terraform\``).

If you are using [Atlas](https://atlas.hashicorp.com), you will need to compile
for Linux amd64 and commit the binary to your terraform project's repository:
```bash
env GOOS=linux GOARCH=amd64 go build -o ~/my/tf-repo/terraform-provider-dns
```

## Example

The following example creates an AWS security group to restrict egress to
https://example.com. The egress rule uses the IP addresses that
resolved locally to `example.com` during the terraform plan.

```
resource "dns_a_record" "example" {
  name = "example.com"
  sort = true
}

resource "aws_security_group" "example" {
  name = "example.com"
  description = "Allow egress to https://example.com"

  egress {
    from_port = "443"
    to_port = "443"
    protocol = "tcp"
    cidr_blocks = [
      "${formatlist("%s/32", dns_a_record.example.addrs))}"
    ]
  }
}
```
