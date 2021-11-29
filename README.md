# iam
AWS Identity and Access Management (IAM) Terraform module
Features
Cross-account access. Define IAM roles using iam_assumable_role or iam_assumable_roles submodules in "resource AWS accounts (prod, staging, dev)" and IAM groups and users using iam-group-with-assumable-roles-policy submodule in "IAM AWS Account" to setup access controls between accounts. See iam-group-with-assumable-roles-policy example for more details.
Individual IAM resources (users, roles, policies). See usage snippets and examples listed below.
Usage
iam-account:

module "iam_account" {
  source  = "terraform-aws-modules/iam/aws//modules/iam-account"
  version = "~> 4.3"

  account_alias = "awesome-company"

  minimum_password_length = 37
  require_numbers         = false
}
iam-assumable-role:

module "iam_assumable_role" {
  source  = "terraform-aws-modules/iam/aws//modules/iam-assumable-role"
  version = "~> 4.3"

  trusted_role_arns = [
    "arn:aws:iam::307990089504:root",
    "arn:aws:iam::835367859851:user/anton",
  ]

  create_role = true

  role_name         = "custom"
  role_requires_mfa = true

  custom_role_policy_arns = [
    "arn:aws:iam::aws:policy/AmazonCognitoReadOnly",
    "arn:aws:iam::aws:policy/AlexaForBusinessFullAccess",
  ]
  number_of_custom_role_policy_arns = 2
}
iam-assumable-role-with-oidc:

module "iam_assumable_role_with_oidc" {
  source  = "terraform-aws-modules/iam/aws//modules/iam-assumable-role-with-oidc"
  version = "~> 4.3"

  create_role = true

  role_name = "role-with-oidc"

  tags = {
    Role = "role-with-oidc"
  }

  provider_url = "oidc.eks.eu-west-1.amazonaws.com/id/BA9E170D464AF7B92084EF72A69B9DC8"

  role_policy_arns = [
    "arn:aws:iam::aws:policy/AmazonEKS_CNI_Policy",
  ]
  number_of_role_policy_arns = 1
}
iam-assumable-role-with-saml:

module "iam_assumable_role_with_saml" {
  source  = "terraform-aws-modules/iam/aws//modules/iam-assumable-role-with-saml"
  version = "~> 4.3"

  create_role = true

  role_name = "role-with-saml"

  tags = {
    Role = "role-with-saml"
  }

  provider_id = "arn:aws:iam::235367859851:saml-provider/idp_saml"

  role_policy_arns = [
    "arn:aws:iam::aws:policy/ReadOnlyAccess"
  ]
  number_of_role_policy_arns = 1
}
iam-assumable-roles:

module "iam_assumable_roles" {
  source  = "terraform-aws-modules/iam/aws//modules/iam-assumable-roles"
  version = "~> 4.3"

  trusted_role_arns = [
    "arn:aws:iam::307990089504:root",
    "arn:aws:iam::835367859851:user/anton",
  ]

  create_admin_role = true

  create_poweruser_role = true
  poweruser_role_name   = "developer"

  create_readonly_role       = true
  readonly_role_requires_mfa = false
}
iam-assumable-roles-with-saml:

module "iam_assumable_roles_with_saml" {
  source  = "terraform-aws-modules/iam/aws//modules/iam-assumable-roles-with-saml"
  version = "~> 4.3"

  create_admin_role = true

  create_poweruser_role = true
  poweruser_role_name   = "developer"

  create_readonly_role = true

  provider_id   = "arn:aws:iam::235367859851:saml-provider/idp_saml"
}
iam-user:

module "iam_user" {
  source  = "terraform-aws-modules/iam/aws//modules/iam-user"
  version = "~> 4.3"

  name          = "vasya.pupkin"
  force_destroy = true

  pgp_key = "keybase:test"

  password_reset_required = false
}
iam-policy:

module "iam_policy" {
  source  = "terraform-aws-modules/iam/aws//modules/iam-policy"
  version = "~> 4.3"

  name        = "example"
  path        = "/"
  description = "My example policy"

  policy = <<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": [
        "ec2:Describe*"
      ],
      "Effect": "Allow",
      "Resource": "*"
    }
  ]
}
EOF
}
iam-group-with-assumable-roles-policy:

module "iam_group_with_assumable_roles_policy" {
  source  = "terraform-aws-modules/iam/aws//modules/iam-group-with-assumable-roles-policy"
  version = "~> 4.3"

  name = "production-readonly"

  assumable_roles = [
    "arn:aws:iam::835367859855:role/readonly"  # these roles can be created using `iam_assumable_roles` submodule
  ]

  group_users = [
    "user1",
    "user2"
  ]
}
iam-group-with-policies:

module "iam_group_with_policies" {
  source  = "terraform-aws-modules/iam/aws//modules/iam-group-with-policies"
  version = "~> 4.3"

  name = "superadmins"

  group_users = [
    "user1",
    "user2"
  ]

  attach_iam_self_management_policy = true

  custom_group_policy_arns = [
    "arn:aws:iam::aws:policy/AdministratorAccess",
  ]

  custom_group_policies = [
    {
      name   = "AllowS3Listing"
      policy = data.aws_iam_policy_document.sample.json
    }
  ]
}
