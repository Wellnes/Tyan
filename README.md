<p align="center">
  <img src="https://user-images.githubusercontent.com/11923488/209480228-98127c79-7f10-4c54-af72-fdcaf04fee6f.png" />
</p>

# Изменения в конфигурации `BAS Розничная торговля`
### Добавлено: 
  - [ ] отчет `Сверка касс и продаж`
  
     ###### *подсистемы -> Финансы*

  - [ ] общий модуль `МенеджерОборудованияКлиент`
    
```bsl
Процедура НачатьВыгрузкуДанныеВТСД_ПодключениеЗавершение(РезультатПодключения, Параметры) Экспорт
	
	Если РезультатПодключения.Результат Тогда
 
		ВходныеПараметры  = Новый Массив();
		МассивТСД = Новый Массив;
		
		Для Каждого текСтрока Из Параметры.ТаблицаВыгрузкиТоваров Цикл
			Если текСтрока.Свойство("Номенклатура") Тогда
				НаименованиеНоменклатуры = Лев(текСтрока.Номенклатура,40);
			ИначеЕсли текСтрока.Свойство("Наименование") Тогда
				НаименованиеНоменклатуры = Лев(текСтрока.Наименование,40);
			Иначе
				НаименованиеНоменклатуры = "";
			КонецЕсли;
			
			СтрокаМассиваТСД = Новый СписокЗначений;
			
			//Не массив для сохранения совместимости с обработками обслуживания.
			СтрокаМассиваТСД.Добавить(?(текСтрока.Свойство("Штрихкод")                  , Лев(текСтрока.Штрихкод,13), ""));
			СтрокаМассиваТСД.Добавить(?(текСтрока.Свойство("Номенклатура")              , НаименованиеНоменклатуры, ""));
			СтрокаМассиваТСД.Добавить(?(текСтрока.Свойство("ЕдиницаИзмерения")          , текСтрока.ЕдиницаИзмерения, ""));
			СтрокаМассиваТСД.Добавить(?(текСтрока.Свойство("ХарактеристикаНоменклатуры"), текСтрока.ХарактеристикаНоменклатуры, ""));
			СтрокаМассиваТСД.Добавить(?(текСтрока.Свойство("СерияНоменклатуры")         , текСтрока.СерияНоменклатуры, ""));
			СтрокаМассиваТСД.Добавить(?(текСтрока.Свойство("Качество")                  , текСтрока.Качество, ""));
			СтрокаМассиваТСД.Добавить(?(текСтрока.Свойство("Цена")                      , текСтрока.Цена, 0));
			СтрокаМассиваТСД.Добавить(?(текСтрока.Свойство("Количество")                , текСтрока.Количество, 0));
			СтрокаМассиваТСД.Добавить(?(текСтрока.Свойство("Артикул ")                  , текСтрока.Артикул, ""));
			МассивТСД.Добавить(СтрокаМассиваТСД);
		КонецЦикла;
				
		ВходныеПараметры.Добавить("Items");
		ВходныеПараметры.Добавить(МассивТСД);
		ВходныеПараметры.Добавить(Параметры.ПолнаяВыгрузка);
		
		Оповещение = Новый ОписаниеОповещения("НачатьВыгрузкуДанныеВТСД_ВыполнитьКомандуЗавершение", ЭтотОбъект, Параметры);
		НачатьВыполнениеКоманды(Оповещение, Параметры.ИдентификаторУстройства, "UploadDirectory", ВходныеПараметры);
		
	Иначе
		Если Параметры.ОповещениеПриЗавершении <> Неопределено Тогда
			ОписаниеОшибки = РезультатПодключения.ОписаниеОшибки;
			РезультатОперации = ПараметрыВыполненияОперацииНаОборудовании(Ложь, ОписаниеОшибки, Параметры.ИдентификаторУстройства);
			ВыполнитьОбработкуОповещения(Параметры.ОповещениеПриЗавершении, РезультатОперации);
		КонецЕсли;
	КонецЕсли;
	
КонецПроцедуры
```
- [ ] общий модуль `ПолучениеОбновленийПрограммыГлобальный`
```bsl
Процедура ПолучениеОбновленийПрограммы_ПроверитьНаличиеОбновлений() Экспорт
	
	// ПолучениеОбновленийПрограммыКлиент.ПроверитьНаличиеОбновлений();
	
КонецПроцедуры
```
- [ ] общая форма `СтруктураПодчиненностиПереопределяемый`
```bsl
&НаСервере
функция ПолучитьТипОплаты(чек)
	
	строки = чек.Оплата;
	Для Каждого стр Из строки Цикл
		 рез = стр.ВидОплаты;
	КонецЦикла;	 

	Возврат рез;	
	
конецфункции

&НаСервере
функция получитьОплаты(Отч)
	
	суммакарты = 0;
	строки = отч.ОплатаПлатежнымиКартами;
	Для Каждого стр из строки Цикл
		суммакарты = суммакарты+стр.сумма;
	 Конеццикла;
	
	возврат "(Нал."+Отч.СуммаОплатыНаличных+"; Карта "+суммакарты+")";

конецфункции

// Получает представление документа для печати.
//
// Параметры:
//  Выборка  - КоллекцияДанных - структура или выборка из результатов запроса
//                 в которой содержатся дополнительные реквизиты, на основании
//                 которых можно сформировать переопределенное представление 
//                 документа для вывода в отчет "Структура подчиненности".
//
// Возвращаемое значение:
//   Строка,Неопределено   - переопределенное представление документа, или Неопределено,
//                           если для данного типа документов такое не задано.
//
Функция ПредставлениеОбъектаДляВыводаВОтчет(Выборка) Экспорт
	
	ПредставлениеОбъекта = Выборка.Представление;
	Если ТипЗнч( Выборка.Ссылка ) = Тип( "ДокументСсылка.ЧекККМ" ) Тогда
		ПредставлениеОбъекта = ПредставлениеОбъекта + "("+ПолучитьТипОплаты(Выборка.ссылка)+") " + НСтр("ru='на сумму';uk='на суму'") + " " + Выборка.СуммаДокумента + " " + Выборка.Валюта;
		Возврат	ПредставлениеОбъекта;
	ИначеЕсли ТипЗнч( Выборка.Ссылка ) = Тип( "ДокументСсылка.ОтчетОРозничныхПродажах" ) Тогда
		ПредставлениеОбъекта = ПредставлениеОбъекта + " " + НСтр("ru='на сумму';uk='на суму'") + " " + Выборка.СуммаДокумента + " " + Выборка.Валюта + получитьОплаты( выборка.ссылка );
		Возврат	ПредставлениеОбъекта;	
	КонецЕсли;			
	
	Возврат Неопределено;
	
КонецФункции
```
- [ ] документы: `ЗаказПоставщику`, `УстановкаСебестоимости`
- [ ] документ `ЧекККМ`: изменена форма списка, добавлен макет `НашЧекПокупателя`
- [ ] обработка `ВыгрузкаТоваровВТСД`
- [ ] обработка `РМКУправляемыйРежим`

```bsl
&НаСервере
Функция печатьчековнасервере(массивЧеков,ТабДокумент)
	   
	   Для каждого СсылкаНаЧек Из МассивЧеков Цикл 
		   ВысотаЧека = 0; 
		   
		   Макет = документы.ЧекККМ.ПолучитьМакет("НашЧекПокупателя");
		   ОбластьМакета = макет.ПолучитьОбласть("ШапкаЧека");
		   ОбластьМакета.параметры.Заголовок = "ЗООмагазин";
		   ОбластьМакета.Параметры.Организация = "ул. Преображенская 15"; //СсылкаНаЧек.Магазин;
		   ОбластьМакета.Параметры.чек         = СсылкаНаЧек;
		   ТабДокумент.Вывести(ОбластьМакета);	 
		   ВысотаЧека = ВысотаЧека + 52;
		   
		   // тперь товары и скидки из чека
		   ОбластьМакетаТ = макет.ПолучитьОбласть("ТелоЧекаТовар");
		   ОбластьМакетаС = макет.ПолучитьОбласть("ТелоЧекаСкидка");
		   СуммаДокумента = 0;
		   
		   Для каждого стр из  СсылкаНаЧек.Товары цикл
			   СуммаПоСтроке = Окр(Стр.Цена * стр.количество, 2);	
			   СуммаДокумента = СуммаДокумента + СуммаПоСтроке;	
			   ОбластьМакетаТ.параметры.НаименованиеТовара = стр.Номенклатура;
			   ОбластьМакетаТ.параметры.КоличествоЦена     = ""+стр.количество +" х "+стр.цена+" грн.";
			   ТабДокумент.Вывести(ОбластьМакетаТ);
			   ВысотаЧека = ВысотаЧека + 22;
			   // если есть скидка добавим  и выводим
			   
		   конеццикла;
		   // теперь итого
		   ОбластьМакета = макет.ПолучитьОбласть("ПодвалЧека");
		   Областьмакета.Параметры.Итог = "Итого:"+СуммаДокумента+" грн.";
		   ТабДокумент.Вывести(ОбластьМакета);
		   ВысотаЧека = ВысотаЧека + 10;
		   // выводим итоги по скидкам
		   
		   //выводим оплаты
		   
		   // выводим подпись
		   ОбластьМакета = макет.ПолучитьОбласть("ПодвалЧекаПодпись");
		   Областьмакета.Параметры.Сотрудник = СсылкаНаЧек.Продавец;
		   ТабДокумент.Вывести(ОбластьМакета);
		   ВысотаЧека = ВысотаЧека + 60;
		   
		   // сообщить ("печатаю чек"+ссылканачек);	
		   
		   
	   КонецЦикла;
	   
	   // подготовим к печати и отправим на принтер 
	  Возврат ВысотаЧека 
	   
Конецфункции  

&НаКлиенте
Процедура НапечататьЧекиККМ(МассивСсылокЧеков)
	
	ЗаполнитьАдресТаблицыПечатныхФорм(МассивСсылокЧеков);
	
	ПараметрыФормы = Новый Структура;
	ПараметрыФормы.Вставить("АдресТаблицыПечатныхФорм", АдресТаблицыПечатныхФорм);
	
	ДополнительныеПараметры = Новый Структура;
	ДополнительныеПараметры.Вставить("МассивСсылокЧеков", МассивСсылокЧеков);
	
//	ОбработчикОповещения = Новый ОписаниеОповещения("ОповещениеОткрытьФормуВыбораПечатныхФорм", ЭтотОбъект, ДополнительныеПараметры);
//	Режим = РежимОткрытияОкнаФормы.БлокироватьВесьИнтерфейс;
//	ОткрытьФорму("Обработка.РМКУправляемыйРежим.Форма.ФормаВыбораПечатныхФорм", ПараметрыФормы, УникальныйИдентификатор,,,, ОбработчикОповещения, Режим);
	
	//автоматически выводим на печать чек
	ТабДок = Новый ТабличныйДокумент;
	
	высота  = печатьчековнасервере(МассивСсылокЧеков,ТабДок);
	ТабДок.ИмяПринтера="Xprinter-XP-370B";
	ТабДок.КоличествоЭкземпляров = 1;
	ТабДок.РазмерСтраницы="Custom";
	ТабДок.ШиринаСтраницы=78;
	ТабДок.ВысотаСтраницы=высота;
	ТабДок.ОриентацияСтраницы=ОриентацияСтраницы.Портрет;
	ТабДок.ПолеСлева=0;
	ТабДок.ПолеСправа=0;
	ТабДок.ПолеСверху=0;
	ТабДок.ПолеСнизу=0;
	ТабДок.АвтоМасштаб = Истина;
	Попытка
     ТабДок.Напечатать(РежимИспользованияДиалогаПечати.Использовать);
    Исключение
     Сообщить("Включите принтер");
    КонецПопытки;
	
КонецПроцедуры

&НаКлиенте
Процедура ЗавершитьОплатуТоваров(Печать, ЗадатьВопросОПотереСуммыПоПодарочнымСертификатам = Истина)
	
	...	

	ПечатьПослеПробитияЧека = Истина;
	//ПечатьПослеПробитияЧека = Печать;
	
	ПодключитьОбработчикОжидания("ЗавершитьОплатуТоваровПослеВыводаСдачи", 0.1, Истина);
	
КонецПроцедуры

&НаКлиенте
Процедура ЗавершитьОплатуТоваровПослеВыводаСдачи()
	
	НазначитьАвтоматическиеСкидкиКлиент(Истина);
	
	Отказ = Ложь;
	СоздатьЧеки(Отказ, ПечатьПослеПробитияЧека);

	ПечатьПослеПробитияЧека = Истина; // была ложь
	
	...
	
КонецПроцедуры	
```
