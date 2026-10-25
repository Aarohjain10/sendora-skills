# Allowed values for prospect filters

Values are case sensitive and exact. A wrong value is rejected with a 422 that names it. When unsure, run `parse_criteria` on a plain-English description and let it pick values.

## company_type

`Educational`, `Educational Institution`, `Government Agency`, `Nonprofit`, `Partnership`, `Privately Held`, `Public Company`, `Self-Employed`, `Self-Owned`, `Sole Proprietorship`

## employee_range

`1-10`, `11-50`, `51-200`, `201-500`, `501-1000`, `1001-5000`, `5001-10000`, `10001+`

## funding_type

`Series unknown`, `Seed`, `Series A`, `Series B`, `Series C`, `Series D`, `Series E-J`, `Grant`, `Angel`, `Private equity`, `Debt financing`, `Non equity assistance`, `Post IPO equity`, `Post IPO debt`, `Product crowdfunding`, `Equity crowdfunding`, `Corporate round`, `Convertible note`, `Secondary market`, `Initial coin offering`, `Post IPO secondary`

## job_function

`Advertising & Marketing`, `Art, Culture and Creative Professionals`, `Construction`, `Customer/Client Service`, `Education`, `Engineering`, `Finance & Accounting`, `General Business & Management`, `Healthcare & Human Services`, `Human Resources`, `Information Technology`, `Legal`, `Manufacturing & Production`, `Operations`, `Other`, `Public Administration & Safety`, `Purchasing`, `Research & Development`, `Sales & Business Development`, `Science`, `Supply Chain & Logistics`, `Writing/Editing`

## job_level

`C-Team`, `Director`, `Manager`, `Other`, `Staff`, `VP`

## continent

`Africa`, `Antarctica`, `Asia`, `Europe`, `North America`, `Oceania`, `South America`

## sales_region

`NORAM`, `LATAM`, `EMEA`, `APAC`

## job_seniority

`0-2`, `2-5`, `5-10`, `10+`

## employment_type

`FULL_TIME`, `PART_TIME`, `CONTRACTOR`, `TEMPORARY`, `INTERN`, `VOLUNTEER`, `PER_DIEM`, `OTHER`

## work_arrangement

`On-site`, `Hybrid`, `Remote OK`, `Remote Solely`

