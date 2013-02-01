# PropertyLegend

!SLIDE

# PropertyLegend

## API Driven Forms

### Caleb Woods

!SLIDE left

# The Problem

* API with large amount data
* Complex validations
* Want to keep all validation in one place

!SLIDE left

# Solution: PropertyLegend

* Describes every object, relationship, and property
* Dynamically built from object model

!SLIDE left

# Example

```js
"source": {
  "units":  ["Meteorological","Nutrients","Chem/Fert","General","Pollutants","Wildlife"],
  "value": {
    "type": "HashedPicklist",
    "allowed_values": {
      "Meteorological":["Frost","Hail","High temperatures"],
      "Nutrients":["Oxygen deficiency","Acidity, high","Alkalinity, high"]
    },
    "options": { "allow_blank": true }
  }
},
life_stage": {
  "units": null,
  "value": {
    "type": "Picklist",
    "allowed_values": ["eggs","small larvae","large larvae","nymphs"],
    "options": { "allow_blank": true}
  }
},
"comments": {
  "units": null,
  "value": {
    "type": "Text",
    "options": {}
  }
}
```

!SLIDE left

# Benefits to Rails Code

* Put names to complex validations (Custom Validators)
* Objects have to be able to describe themselves (Smaller)
* Easier to test
* Single medium to discussing API that is language agnostic

!SLIDE left

# Previous Code

```ruby
class CropInjury < ActiveRecord::Base

  include AssignApiAttributes

  belongs_to :scout, touch: true
  belongs_to :created_by, class_name: 'User'

  validates_inclusion_of :category, allow_blank: true, in: %w(Chem/Fert General)
  validates_inclusion_of :units, allow_blank: true, in: lambda { |injury| units }
  validates_numericality_of :amount, unless: :units_is_scale, allow_blank: true
  validate :validate_scale, if: :units_is_scale
  validate :validate_source

  attr_protected :scout_id, :created_by_id

  before_save :set_order
  scope :by_order, order("crop_injuries.order ASC")

  private
  def units_is_scale
    self.units.try(:include?, "Scale")
  end

  def set_order
  def scales
  def validate_scale
  def sources
  def validate_source
  def self.units

end
```

!SLIDE left

# Refactored

```ruby
class CropInjury < ActiveRecord::Base

  include AssignApiAttributes
  include Orderable
  include Properties

  belongs_to :scout, touch: true
  belongs_to :created_by, class_name: 'User'

  attr_protected :scout_id, :created_by_id, :properties

  property :source, class_name: "CropInjurySource"
  property :amount, class_name: "CropInjuryAmount"

end
```
