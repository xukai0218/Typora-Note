1.116.72.172:27017

mongouser
Profound7_boca1_slush_Cite_chert



```
//db.scheme_field_bms.find({
//    $and: [{
//        "companyId": 0
//    }, {
//        "schemeId": 3015
//    }, {
//        "pageWidgetId": {
//            $in: [9276,9283,12100002]
//        }
//    }]
//});



db.scheme_field_bms.deleteMany({
    $and: [{
        "companyId": 0
    }, {
        "schemeId": 3015
    }, {
        "pageWidgetId": {
            $in: [9276,9283,12100002]
        }
    }]
});
db.scheme_field_bms.find({
    $and: [
        {
            "companyId": 0
        },
        {
            "pageWidgetId": {
                $in: [9249,9255,9261,12100012,12100082,12100085,12100101]
            }
        }
    ]
});




db.scheme_field_bms.insert( {
    _id: ObjectId("61e0d12f98a1d5c98b5b095d"),
    id: NumberLong("12104557"),
    companyId: NumberLong("0"),
    schemeId: NumberLong("1"),
    pageWidgetId: NumberLong("9261"),
    schemeJson: [
```