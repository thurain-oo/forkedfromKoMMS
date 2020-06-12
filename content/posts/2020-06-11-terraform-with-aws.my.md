---
title: Terraform ဖြင့် AWS မှာ Infrastructure တည်ဆောက်ခြင်း
author: minn
type: post
date: 2020-06-11T08:51:17+00:00
tags:
  - aws
  - terraform
  - cloud
  - orchestration
  - iaas

---

မည်သည့် resource မှမရှိသော default VPC နှင့် us-east-1 region အတွင်းမှာတည်ဆောက်မည်။

မိမိ၏ စက်ပေါ်တွင် [Terraform](https://www.terraform.io/downloads.html)  ကို ပထမဦးစွာ ထည့်သွင်းထားပါ။

`awscli` ကို မိမိ test လုပ်မည့် AWS account ဖြင့်အလုပ်လုပ်နိုင်ရန် ပြင်ဆင်ပါမည်။ စမ်းသပ်မှုပြုလုပ်မည့် AWS Settings (`profile`) ကို `sandbox` ဟုခေါ်ပါမည်။

```
$ aws --profile sandbox configure
AWS Access Key ID [None]: ENTER-YOUR-ACCESS-KEY-HERE
AWS Secret Access Key [None]: ENTER-YOUR-SECRET-KEY-HERE
Default region name [None]: us-east-1
Default output format [None]: 
```

မိမိ၏ project folder ထဲတွင် `aws.tf` ဖိုင်တစ်ဖိုင် ရေးပါမည်။
```
provider "aws" {
  profile    = "sandbox"
  region     = "us-east-1"
}

resource "aws_instance" "web-server" {
  ami           = "ami-09d95fab7fff3776c"
  instance_type = "t2.micro"
}
```
- အရှေ့တွင် setup လုပ်ခဲ့သော `sandbox` profile ကို အသုံးပြုမည်။
- us-east-1 region တွင် တည်ဆောက်ယူမည်ဖြစ်သည်။
- Amazon Linux 2 AMI ကို အသုံးပြုမည်။
- t2.micro အမျိုးအစား instance ကိုပြုလုပ်မည်။
- ယခုဖန်တီးမည့် instance ကို terraform config ထဲတွင် `web-server` ဟု ရည်ညွှန်းမှတ်ယူမည်။


`aws.tf` ဖိုင် ဖန်တီးပြီးလျှင် terraform ကို ကနဦး အသုံးပြုနိုင်ရန် စီမံပါမည်။

```
$ terraform init
```

Infrastructure တည်ဆောက်မည့် plan ကို အရင်ကြည့်ပါ။ 
```
$ terraform plan
```
Terraform မှ ဘယ် resource item တွေကို AWS ပေါ်မှာ ဖန်တီးသွားမလဲဆိုတာ ချပြပါမည်။

Infrastructure ကို စတင် တည်ဆောက်ခိုင်းပါတော့မည်။
```
$ terraform apply
```
Terraform မှ ဆောင်ရွက်မည့် အစီအစဥ်များကို ချပြပါမည်။ သဘောတူပါက `yes` လို့ဖြေပြီး ရှေ့ဆက်ခိုင်းပါ။ ခဏကြာလျှင် ဖန်တီးယူမှုပြီးဆုံးမည်ဖြစ်ပြီး အောက်ပါ စာသားများကို အဆုံးသတ်တွင် တွေ့နိုင်ပါသည်။

```
[...]
aws_instance.web-server: Creating...
aws_instance.web-server: Still creating... [10s elapsed]
aws_instance.web-server: Still creating... [20s elapsed]
aws_instance.web-server: Still creating... [30s elapsed]
aws_instance.web-server: Creation complete after 36s [id=i-03429b9a3aa0827d8]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```
AWS ၏ Web management မှ EC2 Dashboard ကိုသွားကြည့်လျှင် မိမိ `aws.tf` တွင်ရေးထားသည့်အတိုင်း instance တစ်ခု (Amazon Linux 2 ထည့်ထားသော စက်တစ်လုံး) စတင်ပြီး အသုံးပြနိုင်ရန် အဆင်သင့်ဖြစ်နေပြီ ဖြစ်ကြောင်းတွေ့နိုင်ပါသည်။ `awscli` ကို အသုံးပြု၍လည်း ကြည့်နိုင်ပါသည်။
```
$ aws --profile sandbox ec2 describe-instances
```

Server instance ပြုလုပ်ပြီး နောက်တစ်ဆင့်မှာ  အထဲကို SSH အသုံးပြုပြီး ဝင်ကြည့်မည်ဖြစ်သည်။ သို့သော် မိမိမှာ SSH key များ အဆင်သင့်မရှိသေးပါ။ AWS မှ Linux စက်ထဲ SSH ဖြင့်ဝင်နိုင်ဖို့ စက်ကို စတင်ဖန်တီးချိန်တွင် SSH public key ကို ထည့်သွင်းပေးရမည်ဖြစ်သည်။ ယခုမှ ထည့်သွင်းဖို့ မဖြစ်နိုင်တော့ပါ။ နောက်ကျသွားပါပြီ။ ကျွန်ုပ်တို့ ဖန်တီးခဲ့သည့် အဆင့်များ မှားသွားခဲ့ပါပြီ။ နောက်တစ်ကြိမ် ပြန်လုပ်ပါမည်။ ဤအကြိမ်တွင် SSH public key ကို စက်တည်ဆောင်ချိန်မှာ ထည့်သွင်းပေးရန် `aws.tf` ဖိုင်ထဲတွင် ပြန်ရေးမည်ဖြစ်သည်။

အစမှ ပြန်ဖန်တီးရန် ပြုလုပ်ခဲ့ပြီးသော infrastructure ကို ဖျက်ဆီးပြစ်ပါမည်။

```
$ terraform destroy
```

SSH Key တွေကို ဖန်တီးကြပါစို့။ Key ကို ထုတ်ယူလျှင် private key နှင့် public key ဟူ၍ key နှစ်မျိုးထွက်လာမည်။ public key ကို AWS ပေါ်တင်ပေးပြီး private key ကို မိမိစက်ထဲမှာ လျှို့ဝှက်စွာထားရမည်။
```
$ ssh-keygen -i rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/demo/.ssh/id_rsa): /Users/demo/.ssh/terraform
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in terraform.
Your public key has been saved in /Users/demo/.ssh/terraform.pub.
The key fingerprint is:
SHA256:xa7A8FNYqX0d8CwwUJGh03MyGrvcNmrzZlxJRXn9qbo
The key's randomart image is:
+---[RSA 3072]----+
|      .oB=.o.. . |
|       =o+ o+ . .|
|    . =o* =ooo  o|
|     +.*.Bo..  ..|
|      B So..  .  |
|     . = .o  .   |
|      o.=.  .    |
|      oo+. .     |
|     ..=.  E.    |
+----[SHA256]-----+
```
အထက်ပါ output များကို မြင်ရပြီဆိုလျှင် ကျွန်ုပ်တို့ `terraform` အမည်ရှိသော private key နှင့် `terraform.pub` အမည်ရှိသော public key တို့ကို `/Users/demo/.ssh/` folder ထဲတွင် ရောက်ရှိနေပြီဖြစ်သည်။ ယင်း ဖိုင်များထဲမှ `terraform.pub` ဖိုင်ကို AWS သို့ ပို့ရမည်ဖြစ်ပြီး AWS မှ EC2 instance တည်ဆောက်ချိန်‌တွင် ထည့်သွင်း အသုံးပြုသွားမည်ဖြစ်သည်။

`aws.tf` ဖိုင်ထဲတွင် အောက်ပါအပိုဒ်ကို ထပ်ထည့်သွင်းပါ။
```
resource "aws_key_pair" "deployer" {
  key_name   = "deployer-key"
  public_key = file("~/.ssh/terraform.pub")
}
```
- AWS Key Pair တစ်ခုကိုဖန်တီးမည်။ `deployer` ဟုရည်ညွှန်းမည်။
- `/Users/demo/.ssh/terraform.pub` ဖိုင်ကို သုံးပါမည်။

`aws.tf` ဖိုင်ထဲတွင်ရှိသော instance resource definition အပိုဒ်တွင် အထက်ပါ `deployer` ဟု သတ်မှတ်ခဲ့သော key ကို အသုံးပြုပါရန် ညွှန်ကြားချက်ပေးပါမည်။
```
resource "aws_instance" "web-server" {
  ami           = "ami-09d95fab7fff3776c"
  instance_type = "t2.micro"
  key_name      = aws_key_pair.deployer.id 
```
အကောင်အထည်ဖော်မည့် အစီအစဥ်ကို ပြန်စစ်ဆေးပါ။
```
$ terraform plan
```

ပြီးလျှင် infrastructure ကို တည်ဆောက်ခိုင်းပါ။
```
$ terraform apply
```
အောင်မြင်စွာ တည်ဆောက်ပြီးပါက မိမိ instance ၏ Public IP ကို ထုတ်ဖော်ကြည့်ပါ။ ထို IP သို့ SSH ဝင်ကြည့်မည်ဖြစ်သည်။
```
$ aws --profile sandbox ec2 describe-instances | grep PublicIp
```
ကျွန်ုပ်တို့ AWS VPC ထဲတွင် အခြား resource များ မရှိသည့်အတွက် အထက်ပါ `awscli` ကိုသုံး၍ ထွက်လာသော output ကို filter လုပ်ကြည့်လို့ရသည်။

မိမိ၏ instance ထဲသို့ SSH ဝင်ကြည့်နိုင်ပါပြီ။
```
$ ssh -i ~/.ssh/terraform ec2-user@[Public IP Address]
```
ထို့ကြောင့် ကိုယ်ပိုင် Security Group တစ်ခုတည်ဆောက်ပြီး instance ပေါ်မှ ချထားမည်။ မိမိ၏ Security Group သည် ပြင်ပမှ SSH ချိတ်ဆက်မှုများကို ခွင့်ပြမည်ဖြစ်သည်။

`aws.tf` ဖိုင်ထဲသို့ ယင်း Security Group အသစ်ကို ထည့်သွင်းရေးသားကြပါစို့။
```
resource "aws_security_group" "allow-ssh-sg" {
  name        = "public-ssh-sg"
  description = "Allow incoming ssh traffic"

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

ထိုဖိုင်ထဲတွင်ရှိနေသော instance definition စာပိုဒ်ကို အထက်ပါ Seurity Group ကို အသုံးပြုရန် ပြင်ဆင်ပါ။

```
resource "aws_instance" "web-server" {
  ami           = "ami-09d95fab7fff3776c"
  instance_type = "t2.micro"
  key_name               = aws_key_pair.deployer.id
  vpc_security_group_ids = [aws_security_group.ssh-sg.id]
}
```

Infrastructure ကိုပြန်၍ တည်ဆောက်ခိုင်းပါ။ Terraform သည် အသစ်ထည့်ထားသော resource များနှင့် ပြင်ဆင်ရမည့် resource များကိုသာ ပြုပြင်တည်ဆောက်သွားမည်ကို သတိပြုနိုင်သည်။ အစမှ အဆုံး ပြန်လုပ်သည်မဟုတ်ပါ။
```
$ terraform apply
```

SSH ဝင်ကြည့်ပါ။ Connection ချိတ်ဆက်မိသွားမည်ဖြစ်သည်။
```
$ ssh -i ~/.ssh/terraform ec2-user@[Public IP Address]
```

ယခု infrasture ကို ဆက်လက် အသုံးမလိုတော့ပါက ဖျက်ဆီးပြစ်နိုင်ပါသည်။ `terraform` ဖိုင်ကို အသုံးပြုပြီး အချိန်မရွေး ပြန်လည်‌တည်ဆောက်ချင်လည်း ရပါမည်။
```
$ terraform destroy
```
