https://blog.gruntwork.io/terraform-tips-tricks-loops-if-statements-and-gotchas-f739bbae55f9

resource "aws_instance" "example" {
  count = 3
  ami = "ami-2d39803a"
  instance_type = "t2.micro"
}


resource "aws_instance" "example" {
  count = 3
  ami = "ami-2d39803a"
  instance_type = "t2.micro"
  tags {
    Name = "example-${count.index}"
  }
}




variable "azs" {
  description = "Run the EC2 Instances in these Availability Zones"
  type = "list"
  default = ["us-east-1a", "us-east-1b", "us-east-1c"]
}
resource "aws_instance" "example" {
  count = "${length(var.azs)}"
  ami = "ami-2d39803a"
  instance_type = "t2.micro"
  availability_zone = "${element(var.azs, count.index)}"
  tags {
    Name = "example-${count.index}"
  }
}


Acceder a un campo determinado de una lista de elementos:
element(aws_subnet.foo.*.id, count.index)


# for_each
https://developer.hashicorp.com/terraform/language/meta-arguments/for_each

resource "aws_iam_user" "the-accounts" {
  for_each = toset( ["Todd", "James", "Alice", "Dottie"] )
  name     = each.key
}


# for
https://developer.hashicorp.com/terraform/language/expressions/for


Generar un for a partir de dos maps:
for group in concat(
        [for k,v in google_compute_instance_group.vm : v],
        [for k,v in google_compute_instance_group.vm_rep : v],
) :
