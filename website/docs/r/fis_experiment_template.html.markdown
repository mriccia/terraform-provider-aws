---
subcategory: "FIS (Fault Injection Simulator)"
layout: "aws"
page_title: "AWS: aws_fis_experiment_template"
description: |-
  Provides an FIS Experiment Template.
---

# Resource: aws_fis_experiment_template

Provides an FIS Experiment Template, which can be used to run an experiment.
An experiment template contains one or more actions to run on specified targets during an experiment.
It also contains the stop conditions that prevent the experiment from going out of bounds.
See [Amazon Fault Injection Simulator](https://docs.aws.amazon.com/fis/index.html)
for more information.

## Example Usage

```terraform
resource "aws_fis_experiment_template" "example" {
  description = "example"
  role_arn    = aws_iam_role.example.arn

  stop_condition {
    source = "none"
  }

  action {
    name      = "example-action"
    action_id = "aws:ec2:terminate-instances"

    target {
      key   = "Instances"
      value = "example-target"
    }
  }

  target {
    name           = "example-target"
    resource_type  = "aws:ec2:instance"
    selection_mode = "COUNT(1)"

    resource_tag {
      key   = "env"
      value = "example"
    }
  }
}
```

## Argument Reference

The following arguments are required:

* `action` - (Required) Action to be performed during an experiment. See below.
* `description` - (Required) Description for the experiment template.
* `role_arn` - (Required) ARN of an IAM role that grants the AWS FIS service permission to perform service actions on your behalf.
* `stop_condition` - (Required) When an ongoing experiment should be stopped. See below.

The following arguments are optional:

* `tags` - (Optional) Key-value mapping of tags. If configured with a provider [`default_tags` configuration block](/docs/providers/aws/index.html#default_tags-configuration-block) present, tags with matching keys will overwrite those defined at the provider-level.
* `target` - (Optional) Target of an action. See below.

### `action`

* `action_id` - (Required) ID of the action. To find out what actions are supported see [AWS FIS actions reference](https://docs.aws.amazon.com/fis/latest/userguide/fis-actions-reference.html).
* `name` - (Required) Friendly name of the action.
* `description` - (Optional) Description of the action.
* `parameter` - (Optional) Parameter(s) for the action, if applicable. See below.
* `start_after` - (Optional) Set of action names that must complete before this action can be executed.
* `target` - (Optional) Action's target, if applicable. See below.

#### `parameter`

* `key` - (Required) Parameter name.
* `value` - (Required) Parameter value.

For a list of parameters supported by each action, see [AWS FIS actions reference](https://docs.aws.amazon.com/fis/latest/userguide/fis-actions-reference.html).

#### `target` (`action.*.target`)

* `key` - (Required) Target type. Valid values are `Clusters` (ECS Clusters), `DBInstances` (RDS DB Instances), `Instances` (EC2 Instances), `Nodegroups` (EKS Node groups), `Roles` (IAM Roles).
* `value` - (Required) Target name, referencing a corresponding target.

### `stop_condition`

* `source` - (Required) Source of the condition. One of `none`, `aws:cloudwatch:alarm`.
* `value` - (Optional) ARN of the CloudWatch alarm. Required if the source is a CloudWatch alarm.

### `target`

* `name` - (Required) Friendly name given to the target.
* `resource_type` - (Required) AWS resource type. The resource type must be supported for the specified action. To find out what resource types are supported, see [Targets for AWS FIS](https://docs.aws.amazon.com/fis/latest/userguide/targets.html#resource-types).
* `selection_mode` - (Required) Scopes the identified resources. Valid values are `ALL` (all identified resources), `COUNT(n)` (randomly select `n` of the identified resources), `PERCENT(n)` (randomly select `n` percent of the identified resources).
* `filter` - (Optional) Filter(s) for the target. Filters can be used to select resources based on specific attributes returned by the respective describe action of the resource type. For more information, see [Targets for AWS FIS](https://docs.aws.amazon.com/fis/latest/userguide/targets.html#target-filters). See below.
* `resource_arns` - (Optional) Set of ARNs of the resources to target with an action. Conflicts with `resource_tag`.
* `resource_tag` - (Optional) Tag(s) the resources need to have to be considered a valid target for an action. Conflicts with `resource_arns`. See below.

~> **NOTE:** The `target` configuration block requires either `resource_arns` or `resource_tag`.

#### `filter`

* `path` - (Required) Attribute path for the filter.
* `values` - (Required) Set of attribute values for the filter.

~> **NOTE:** Values specified in a `filter` are joined with an `OR` clause, while values across multiple `filter` blocks are joined with an `AND` clause. For more information, see [Targets for AWS FIS](https://docs.aws.amazon.com/fis/latest/userguide/targets.html#target-filters).

#### `resource_tag`

* `key` - (Required) Tag key.
* `value` - (Required) Tag value.

## Attributes Reference

In addition to all arguments above, the following attributes are exported:

* `id` - Experiment Template ID.

## Import

FIS Experiment Templates can be imported using the `id`, e.g.

```
$ terraform import aws_fis_experiment_template.template EXT123AbCdEfGhIjK
```
