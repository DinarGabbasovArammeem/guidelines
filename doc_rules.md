Python. Документирующие строки для методов
==========================================

Стоки документации должны начинаться на следующей за объявлением метода строчкой в тройных кавычках. Лучше всего руководствоваться следующим порядком пунктов описания метода:

1.  [Краткое описание метода](#Краткое_описание_метода "wikilink")
2.  [Логика метода](#Логика_метода "wikilink")
3.  [Args](#Args "wikilink")
4.  [Triggers](#Triggers "wikilink")
5.  [Attrs Update](#Attrs_Update "wikilink")
6.  [Methods](#Methods "wikilink")
7.  [Workflow signals](#Workflow_signals "wikilink")
8.  [Returns](#Returns "wikilink")
9.  [Raises](#Raises "wikilink")
10. [Extra Info](#Extra_Info "wikilink")

Пример:

        def onchange_partner_id(self, cr, uid, ids, part, context=None):
            """
            Onchange (extend) method for the attribute partner_id.
            
            Call the superclass method firstly. Then search partner with same name but with type 'delivery' or 'invoice'
            to set them as Delivery and Invoice addresses respectivelty.

            Args:
             * cr - cursor
             * uid(int) - user id
             * ids(list) - list of obj ids
             * part(res.partner) - sale.order customer id
             * context(dict) - context

            Attrs Update:
             * partner_shipping_id: same partner with type 'delivery' if exist. Partner otherwise.
             * partner_invoice_id: same partner with type 'invoice' if exist. Partner otherwise.

            Returns:
             * dict: with next keys
               * value: dict with (field, new field value)
               * warning: dict with errors
            """
            res = super(sale_order, self).onchange_partner_id(cr, uid, ids, part=part, context=context)
            res['value']['user_id'] = uid
            res_partner = self.pool.get('res.partner')
            partner_id = res_partner.browse(cr, uid, [part])
            delivery_partner = res_partner.search(
                cr, uid, [('name', '=', partner_id.name), ('type', '=', 'delivery')], limit=1,
            )
            if delivery_partner:
                res['value']['partner_shipping_id'] = delivery_partner[0]
            invoice_partner = res_partner.search(
                cr, uid, [('name', '=', partner_id.name), ('type', '=', 'invoice')], limit=1,
            )
            if invoice_partner:
                res['value']['partner_invoice_id'] = invoice_partner[0]
            return res

Если функция очень простая, и её можно описание, отражающее все необходимы ключевые моменты, укладывается в строку длинной 100 символов, то такого описания будет достаточно. Пример:

        def is_number(s):
            """
            Return True if incoming string can be converted to the int, False otherwise.
            """
            try:
                int(s)
                return True
            except ValueError:
                return False

Краткое описание метода
-----------------------

Для чего используется метод

### Примеры

        @api.one
        def _inverse_state(self):
            """
            Inverse method for the attribute state.
            ...
            """
            if self.state == 'q_confirm':
                self.action_ship_create()
            if not self.partner_id.have_successfull_so and self.state not in ['draft', 'sent']:
                self.partner_id.have_successfull_so = True
            self._change_first_last_sentence()

        def onchange_partner_id(self, cr, uid, ids, part, context=None):
            """
            Onchangee (extend) method for the attribute partner_id.
            ...
            """
            res = super(sale_order, self).onchange_partner_id(cr, uid, ids, part=part, context=context)
            res['value']['user_id'] = uid
            res_partner = self.pool.get('res.partner')
            partner_id = res_partner.browse(cr, uid, [part])
            delivery_partner = res_partner.search(
                cr, uid, [('name', '=', partner_id.name), ('type', '=', 'delivery')], limit=1,
            )
            if delivery_partner:
                res['value']['partner_shipping_id'] = delivery_partner[0]
            invoice_partner = res_partner.search(
                cr, uid, [('name', '=', partner_id.name), ('type', '=', 'invoice')], limit=1,
            )
            if invoice_partner:
                res['value']['partner_invoice_id'] = invoice_partner[0]
            return res

        @api.one
        def backend_onchanges_run(self):
            """
            Call onchange methods for update a sale order data from backend. Special for orders created from parsers.
            ...
            """
            self._change_first_last_sentence()
            self._change_shipping_delivery_payment()
            self._change_shipping_term()
            self._change_delivery_type()
            self.put_print_payment()
            # self._delete_tax_in_order_lines()

            if self.state == 'q_confirm':
                self.action_ship_create()

Логика метода
-------------

Основная суть вычислений и манипуляций

### Примеры

        @api.one
        def _inverse_state(self):
            """
            ...
            Mark partner as customer with successfull sale orders in state higher \"Quotation Sent\".
            Call method _change_first_last_sentence to update first and last sentences of printings.
            ...
            """
            if self.state == 'q_confirm':
                self.action_ship_create()
            if not self.partner_id.have_successfull_so and self.state not in ['draft', 'sent']:
                self.partner_id.have_successfull_so = True
            self._change_first_last_sentence()

        def onchange_partner_id(self, cr, uid, ids, part, context=None):
            """
            ...
            Call the superclass method firstly. Then search partner with same name but with type 'delivery' or 'invoice'
            to set them as Delivery and Invoice addresses respectivelty.
            ...
            """
            res = super(sale_order, self).onchange_partner_id(cr, uid, ids, part=part, context=context)
            res['value']['user_id'] = uid
            res_partner = self.pool.get('res.partner')
            partner_id = res_partner.browse(cr, uid, [part])
            delivery_partner = res_partner.search(
                cr, uid, [('name', '=', partner_id.name), ('type', '=', 'delivery')], limit=1,
            )
            if delivery_partner:
                res['value']['partner_shipping_id'] = delivery_partner[0]
            invoice_partner = res_partner.search(
                cr, uid, [('name', '=', partner_id.name), ('type', '=', 'invoice')], limit=1,
            )
            if invoice_partner:
                res['value']['partner_invoice_id'] = invoice_partner[0]
            return res

Args
----

Список входящий параметров с их описанием, по возможности указывать тип параметров.

### Пример

        def onchange_partner_id(self, cr, uid, ids, part, context=None):
            """
            ...
            Args:
             * cr - cursor
             * uid(int) - user id
             * ids(list) - list of obj ids
             * part(res.partner) - sale.order customer id
             * context(dict) - context
            ...
            """

Triggers
--------

Список зависимостей метода, по изменению каких параметров будет вызван метод

### Примеры

        @api.one
        @api.depends(
            'order_line.volume_weight',
            'order_line.product_uom_qty',
        )
        def _compute_volume_weight(self):
            """
            ...
            Triggers:
             * order_line.volume_weight
             * order_line.product_uom_qty
            ...
            """

        @api.onchange('shipping_terms')
        def _change_shipping_term(self):
            """
            ...
            Triggers:
             * shipping_terms
            ...
            """
            ...

Attrs Update
------------

Список изменяемых в функции параметров с кратким описанием логики рассчета

### Пример

        @api.one
        def _change_shipping_delivery_payment(self):
            """
            ...
            Attrs Update:
             * shipping_terms: section_id.default_shipping_id if setted and print_form=='base'. Call _set_new_settings_for_printings otherwise.
             * delivery_time: section_id.default_delivery_id if setted. 'eleo_crm_sale.delivery_individual_manufacturing' otherwise.
             * payment_term_eleo: section_id.default_payment_id if setted. 'eleo_crm_sale.payment_term_1_3' otherwise.
            ...
            """
            if self.section_id:
                if self.print_form == 'base' and self.section_id.default_shipping_id:
                    self.shipping_terms = self.section_id.default_shipping_id
                else:
                    self._set_new_settings_for_printings()

                if self.section_id.default_delivery_id:
                    self.delivery_time = self.section_id.default_delivery_id
                else:
                    self.delivery_time = self.env.ref('eleo_crm_sale.delivery_individual_manufacturing')
                if self.section_id.default_payment_id:
                    self.payment_term_eleo = self.section_id.default_payment_id
                else:
                    self.payment_term_eleo = self.env.ref('eleo_crm_sale.payment_term_1_3')

Methods
-------

Список методов, которые запускает данная функция. Сюда не входят методы вызываемые для вычисления каких-либо параметров.

### Пример

        @api.one
        def backend_onchanges_run(self):
            """
            ...
            Methods:
             * _change_first_last_sentence
             * _change_shipping_delivery_payment
             * _change_shipping_term
             * _change_delivery_type
             * put_print_payment
             * action_ship_create (only if the state is \"Quotation Confirmed\")
            ...
            """
            self._change_first_last_sentence()
            self._change_shipping_delivery_payment()
            self._change_shipping_term()
            self._change_delivery_type()
            self.put_print_payment()
           
            if self.state == 'q_confirm':
                self.action_ship_create()

Workflow signals
----------------

### Пример

        @api.multi
        def action_button_quotation_confirm(self):
            """
            ...
            Workflow signals:
             * quotation_confirm
            ...
            """
            self.ensure_one()
            self.signal_workflow('quotation_confirm')
            if self.lead_special:
                self.sudo().lead_special.case_mark_won()
            self.date_order = fields.datetime.now()

Returns
-------

Описание выходных параметров метода в формате "\* тип возвращаемого аргумента: логическое значение аргумента"

### Примеры

        def onchange_partner_id(self, cr, uid, ids, part, context=None):
            """
            ...
            Returns:
             * dict: with next keys
               * value: dict with (field, new field value)
               * warning: dict with errors
            ...
            """
            res = super(sale_order, self).onchange_partner_id(cr, uid, ids, part=part, context=context)
            res['value']['user_id'] = uid
            ...
            return res

        @api.one
        def _amount_line_tax(self, line):
            """
            ...
            Returns:
             * float: size of tax for incoming line
            """
            val = 0.0
            for c in line.tax_id.compute_all(
                    line.price_unit * (1 - (line.discount or 0.0) / 100.0),
                    line.product_uom_qty,
                    line.product_id,
                    line.order_id.partner_id)['taxes']:
                val += c.get('amount', 0.0)
            return val

Raises
------

Список всех исключений, имеющих отношение к интерфейсу

### Пример

        @api.multi
        def action_button_downpayment(self):
            """
            ...
            Raises:
             * ValueError: Expected Singleton
            ...
            """
            self.ensure_one()
            self.signal_workflow('downpayment_signal')

Extra Info
----------

Особые условия для выполнения функции, тонкие моменты вычислений и т.п.

### Пример

        @api.multi
        def _change_shipping_delivery_payment(self):
            """
            ...
            Extra Info:
             * Don't make any changes in case no section.
             * Expected singleton
            ...
            """
            self.ensure_one()
            if self.section_id:
                if self.print_form == 'base' and self.section_id.default_shipping_id:
                    ...
