          # Invoice_Generator
          Hello, This is a small project for Invoice generator with 3 different option such as 1. Generate invoice, 2. Show saved invoices, 3. Search for invoice. 

          #include<stdio.h>
          #include<stdlib.h>
          #include<string.h>

          void GenerateInvoiceHeader(char customer_name[50]);
          void GenerateInvoiceBody(char item_name[50], int item_quantity, float item_price);
          void GenerateInvoiceFooter(float total);

          struct item_details
          {
              char item_name[50];
              int item_quantity;
              int  item_price;
          };

          struct customer_details
          {
              char customer_name[50];
              int order_quantity;
              struct item_details I[50];
          };

          int main()
          {

              int select_menu;
              struct customer_details C;
              struct customer_details CSI;
              float Total;
              int n;
              char save_bill = 'y';
              char search_name[50];
              FILE * ptr;
              int invoice_found = 0;

              system("cls");
              printf("              INVOICE GENERATOR              \n\n");
              printf("Please select your prefered operation :  \n");
              printf("1. Generate invoice\n");
              printf("2. Show saved invoices\n");
              printf("3. Search for invoices\n");
              printf("4. Exit\n\n");
              printf("Your choice :  ");
              scanf("%d", &select_menu);
              printf("\n\n");
              fflush(stdin);

              if(select_menu == 1)
              {
                  system("cls");
                  printf("Enter name of the customer :  ");
                  fgets(C.customer_name, 50, stdin);
                  printf("\n");
                  printf("Enter the number of items orders :  ");
                  scanf("%d", &n);
                  C.order_quantity = n;
                  printf("\n\n");
                  fflush(stdin);
                  system("cls");

                  for(int i = 0; i < n; i++)
                  {
                      printf("Enter the name of item %d :  ", i+1);
                      gets(C.I[i].item_name);
                      printf("\nEnter the Quantity :  ");
                      scanf("%d", &C.I[i].item_quantity);
                      printf("\nEnter the price :  ");
                      scanf("%d", &C.I[i].item_price);
                      printf("\n");
                      Total += C.I[i].item_quantity * C.I[i].item_price;
                      fflush(stdin);
                  }

                  system("cls");
                  GenerateInvoiceHeader(C.customer_name);
                  for(int i = 0; i < n; i++)
                  {
                      GenerateInvoiceBody(C.I[i].item_name, C.I[i].item_quantity, C.I[i].item_price);
                  }
                  GenerateInvoiceFooter(Total);

                  printf("\nDo you want to save this invoice [y / n] :  ");
                  scanf("%c", &save_bill);

                  if(save_bill == 'y')
                  {
                      ptr = fopen("Saved Invoices.txt", "a+"); 
                      fwrite(&C, sizeof(struct customer_details), 1, ptr);
                      if(fwrite != 0)
                      {
                          printf("\nSuccessfully saved\n");
                      }
                      else
                      {
                          printf("\nError! in saving\n");
                      }
                      fclose(ptr);
                  }
                  else
                  {
                      return 0;
                  }
              }

              if(select_menu == 2)
              {
                  system("cls");
                  ptr = fopen("Saved Invoices.txt", "r");
                  printf("\n\n****************PREVIOUS INVOICES****************\n");
                  while(fread(&CSI, sizeof(struct customer_details), 1, ptr))
                  {
                      float tot = 0;
                      GenerateInvoiceHeader(CSI.customer_name);
                      for(int i = 0; i < CSI.order_quantity; i++)
                      {
                          GenerateInvoiceBody(CSI.I[i].item_name, CSI.I[i].item_quantity, CSI.I[i].item_price);
                      }
                      for(int i = 0; i < CSI.order_quantity; i++)
                      {
                          tot+=CSI.I[i].item_quantity*CSI.I[i].item_price;
                      }
                      GenerateInvoiceFooter(tot);
                  }
                  fclose(ptr);
              }

              if(select_menu == 3)
              {
                  system("cls");
                  printf("\nPlease enter the name of the customer :  ");
                  fgets(search_name, 50, stdin);
                  fflush(stdin);
                  system("cls");
                  ptr = fopen("Saved Invoices.txt", "r");
                  printf("\n\n****************PREVIOUS INVOICES****************\n");
                  while(fread(&CSI, sizeof(struct customer_details), 1, ptr))
                  {
                      float tot = 0;
                      if(!strcmp(search_name, CSI.customer_name))
                      {
                          GenerateInvoiceHeader(CSI.customer_name);
                          for(int i = 0; i < CSI.order_quantity; i++)
                          {
                              GenerateInvoiceBody(CSI.I[i].item_name, CSI.I[i].item_quantity, CSI.I[i].item_price);
                          }
                          for(int i = 0; i < CSI.order_quantity; i++)
                          {
                              tot+=CSI.I[i].item_quantity*CSI.I[i].item_price;
                          }
                          GenerateInvoiceFooter(tot);
                          invoice_found = 1;
                      }

                      if(invoice_found = 0)
                      {
                          printf("Sorry, the invoice for %s does not exist\n", search_name);
                      }
                  }
                  fclose(ptr);
              }

              if(select_menu == 4)
              {
                  return 0;
              }

              return 0;
          }

          void GenerateInvoiceHeader(char customer_name[50])
          {
              printf("                   INVOICE                   \n");
              printf("---------------------------------------------\n");
              printf("Date :  %s\n", __DATE__);
              printf("Invoice to :  %s", customer_name);
              printf("\n---------------------------------------------\n");
              printf("Items\t\t    Qty\t\t    Total\n");
              printf("---------------------------------------------\n");
          }

          void GenerateInvoiceBody(char item_name[50], int item_quantity, float item_price)
          {
              printf("%s\t\t    %d\t\t    %.2f\n", item_name, item_quantity, item_price*item_quantity);
          }

          void GenerateInvoiceFooter(float total)
          {
              float discount = 0.1 * total;
              float net_total = total - discount;
              float CGST = net_total * 0.09;
              float grand_total = net_total + 2*CGST;


              printf("---------------------------------------------\n");
              printf("Sub Total\t\t\t    %.2f\n", total);
              printf("Discount @10\t\t\t    %.2f\n", discount);
              printf("\t\t\t\t    ---------\n");
              printf("Net Total\t\t\t    %.2f\n", net_total);
              printf("CGST @9\t\t\t\t    %.2f\n", CGST);
              printf("SGST @9\t\t\t\t    %.2f\n", CGST);
              printf("---------------------------------------------\n");
              printf("Grand Total\t\t\t    %.2f\n",grand_total);
              printf("---------------------------------------------\n");
          }
