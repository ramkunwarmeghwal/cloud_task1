# cloud_task1
provider "aws" {
  region     = "ap-south-1"
  profile   = "myvimal"
}


resource "aws_instance" "web" {
  ami           = "ami-0447a12f28fddb066"
  instance_type = "t2.micro"
  key_name  = "mykey1111"
  security_groups = [ "launch-wizard-1" ]
  
  connection {
    type     = "ssh"
    user     = "ec2-user"
    private_key = file("C:/Users/Ramkumar/Downloads/mykey1111.pem")
    host     = aws_instance.web.public_ip
  }
   
    provisioner "remote-exec" {
    inline = [
      "sudo yum install httpd php git -y",
      "sudo systemctl restart httpd",
      "sudo systemctl enable httpd",
      
    ]
  }
 
  tags = {
    Name = "corona_os1"
  }
}

resource "aws_ebs_volume" "ebs1" {
  availability_zone = aws_instance.web.availability_zone
  size              = 1

  tags = {
    Name = "lwebs"
  }
}

resource "aws_volume_attachment" "ebs_att" {
  device_name = "/dev/sdh"
  volume_id   = "${aws_ebs_volume.ebs1.id}"
  instance_id = "${aws_instance.web.id}"
  force_detach = true
}

output "myos_ip" {
     value = aws_instance.web.public_ip
}


resource "null_resource"  "nulllremote" {

depends_on = [
    aws_volume_attachment.ebs_att,
  ]

  connection {
    type     = "ssh"
    user     = "ec2-user"
    private_key = file("C:/Users/Ramkumar/Downloads/mykey1111.pem")
    host     = aws_instance.web.public_ip
  }

provisioner "remote-exec" {
    inline = [
      "sudo mkfs.ext4  /dev/xvdh",
      "sudo  mount  /dev/xvdh  /var/www/html/",
       "sudo rm -rf /var/www/html/*",
      "sudo git clone https://github.com/ramkunwarmeghwal/image.git /var/www/html/"
      
    ]
  }
}

/*
resource "aws_s3_bucket" "mybucket" {
  bucket = "task1bucket1234"
  acl    = "public-read"
  
  tags = {
    Name        = "My bucket"
    Environment = "Dev"
  }
}

*/
resource "null_resource"  "local_exec" {
   
  provisioner "local-exec" {
    
      command = " git clone https://github.com/ramkunwarmeghwal/image.git  D:/Screenshots "
  }
}

resource "aws_s3_bucket_object" "folder1" {
    bucket = "${aws_s3_bucket.mybucket.id}"
    acl    = "public-read"
    key    = "sparrow"
    //source = " C:/Users/Ramkumar/Pictures/sparrow.jpg "
    source = "D:/Screenshots/sparrow.jpg"
   content_type = "image/jpg"
}


resource "null_resource"  "nulllremote112" {

depends_on = [
     aws_s3_bucket_object.folder1 ,
  ]

  connection {
    type     = "ssh"
    user     = "ec2-user"
    private_key = file("C:/Users/Ramkumar/Downloads/mykey1111.pem")
    host     = aws_instance.web.public_ip
  }

provisioner "remote-exec" {
    inline = [
      
           "D:/Screenshots"
      
    ]
  }
} 




resource "aws_s3_bucket" "mybucket" {
  bucket = "mybucketbomb"
  acl    = "private"

  tags = {
    Name = "My bucket"
  }
}

locals {
  s3_origin_id = "myS3Origin"
}



resource "aws_cloudfront_distribution" "s3_distribution" {
  origin {
    domain_name = "${aws_s3_bucket.mybucket.bucket_regional_domain_name}"
    origin_id   = "${local.s3_origin_id}"
    
  }

  enabled             = true
  is_ipv6_enabled     = true
  comment             = "Some comment"
 default_root_object = "index.html"

  default_cache_behavior {
    allowed_methods  = ["DELETE", "GET", "HEAD", "OPTIONS", "PATCH", "POST", "PUT"]
    cached_methods   = ["GET", "HEAD"]
    target_origin_id = "${local.s3_origin_id}"

    forwarded_values {
      query_string = false

      cookies {
        forward = "none"
      }
    }

    viewer_protocol_policy = "allow-all"
    min_ttl                = 0
    default_ttl            = 3600
    max_ttl                = 86400
  }

  # Cache behavior with precedence 0
   # Cache behavior with precedence 1


  restrictions {
    geo_restriction {
      restriction_type = "whitelist"
      locations        = ["US", "CA", "GB", "DE"]
    }
  }

  tags = {
    Environment = "production"
  }

  viewer_certificate {
    cloudfront_default_certificate = true
  }
}




resource "null_resource"  "local_exec12" {

    depends_on = [
   aws_s3_bucket_object.folder1 ,
  ]
   
  provisioner "local-exec" {
    
      command = "start chrome https://mybucketbomb.s3.ap-south-1.amazonaws.com/sparrow"
  }
}





