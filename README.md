# echo-binder
一个提供echo中数据binder和validator功能的middleware

## use 
查看example目录

## form interface EXAMPLE
```go
type Price int64

func (p Price) String() string {
	return strconv.Itoa(int(p))
}

func (p *Price) UnmarshalForm(text string) error {
	if text == "" {
		return errors.New("invalid Money text, it should not be empty")
	}
	if dotIndex := strings.IndexByte(text, '.'); dotIndex >= 0 {
		dotFront := text[:dotIndex]
		dotBehind := text[dotIndex+1:]
		switch len(dotBehind) {
		default:
			return fmt.Errorf("invalid Money text: %s", text)
		case 0:
			text = dotFront + "00"
		case 1:
			text = dotFront + dotBehind + "0"
		case 2:
			text = dotFront + dotBehind
		}
	} else {
		text += "00"
	}
	n, err := strconv.ParseInt(text, 10, 64)
	if err != nil {
		return fmt.Errorf("invalid Money text: %s", text)
	}
	*p = Price(n)
	return nil
}
```