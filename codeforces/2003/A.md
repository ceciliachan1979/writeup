# CodeForces contest 2003 (Div2) 第 A 題

問題係[呢度](https://codeforces.com/problemset/problem/2003/A)

呢題冇咩難度，我才係借題發揮，講吓兩個形容詞，necessary（必要） 同 sufficient（充份）。

呢題我哋個目的係要做到個 partitioning，明顯點切都好，第一件一定係 start with 第一個 character，最尾一件一定係 ends with 最後一個 character，要每一對都要 first character 不等於 last character，咁第一個 character **必然** 唔等於最後一個 character。

成個 logic 係
存在 partitioning => 第一個 character 唔等於最後一個 character。

可以寫成目標 => p 嘅 p 都係目標嘅必要條件 (necessary condition)。

英文係咁寫：

In order for the string to have a valid partition, it is **necessary** that the first character is not equal to the last character.

necessary 有時係冇咩用，最經典嘅就係親生阿媽一定要係女人。

之但係咁，如果第一個 character 唔等於最後一個 character，咁就可以咁切，第一個 character 本身係一個 string，其它全部係第二個 string，咁做係 valid，所以只要發現第一個 character 唔等於最後一個 character，就 **足夠** 了。我地成功將個箭咀掉轉咗。

存在 partitioning <= 第一個 character 唔等於最後一個 character。

可以寫成目標 <= p 嘅 p 都係目標嘅充份條件 (sufficient condition)。

英文係咁寫：

To ensure a string does have a valid partition, it is **sufficient** to make sure the first and last characters are not equal.

如果一個 condition 它既 necessary 又 sufficient，其實就係 if-and-only-if 個 case，即係話呢兩個 condition describe 一模一樣嘅嘢。我哋有兩條免費嘅 corollaries。

- 如果頭尾一樣，佢冇 valid partition。
- 如果一條 string 冇 valid partition，佢頭尾一定一樣。

第二條 corollary 唔係咁易直接證嘅。

Code 好明顯係 trivial，唔寫了。